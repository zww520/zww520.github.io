---
title: 使用PFFFT库计算实信号频率幅度谱
date: 2026-06-14 15:42:42
categories:
    - 信号处理
tags: 
    - 信号处理
    - C++
mathjax: true
---

## 使用PFFFT库计算实数信号频率幅度谱

使用PFFFT库计算实数信号的频率幅度谱

<!-- more -->

### PFFFT库介绍

一个专注于一维单精度 FFT 的、速度优先的、跨平台 SIMD 加速的、采用宽松 BSD 许可证的开源FFT计算库

开源地址：https://github.com/marton78/pffft

### 幅度谱计算流程

#### 1. 正弦波实数信号生成

生成指定采样率，幅度，频率和长度的正弦波信号

```
void generateSineWave(double* buffer, int length, double frequency, double samplerate, double amplitude, double phase)
{
    for (int i = 0; i < length; i++) {
        double t = i / samplerate;                    // 时间（秒）
        double angle = 2.0 * M_PI * frequency * t + phase;  // 角度（弧度）
        buffer[i] = amplitude * std::sin(angle);         // 正弦值
    }
}
```

#### 2. 信号直流分量消除

直流分量为FFT后第一个点的幅度，相当于信号波形在幅度轴上的偏移值，通过减去信号均值消除

```
double mean = 0.0;
for (int i = 0; i < length; i++) {
    mean += buffer[i]; // 计算平均值
}
mean /= length; // 平均值
for (int i = 0; i < length; i++) {
    inputBuffer[i] = buffer[i] - mean; // 去掉直流分量
}
```

#### 3. 信号加窗

##### 为什么要加窗：

FFT 在数学上默认假设其处理的信号是**无限长且周期性的**。也就是说，它会把你提供的 N 个样本点看作是原始信号的一个完整周期，然后无限地复制这个片段

**理想情况**：如果你的信号（比如一个正弦波）正好是整周期采样（即信号频率是频率分辨率 `fs/N` 的整数倍），那么复制后的波形在连接处是光滑、连续的，FFT 结果会很完美，只在信号频率处有一条干净的谱线

**现实情况**：大多数情况下，你的信号不会是整周期的。当你截取一段非整周期的信号并复制它时，连接处会产生**突变**（不连续）。这个突变包含了非常丰富的频率成分，就像“打了一个响指”，其能量会“泄漏”到整个频域的其他频率点上，形成一个宽而矮的“裙边”

##### 加窗原理：

窗函数是一个在两端都平滑地衰减到 0 的序列

**它的作用是：**
当你把原始信号 **x[n]** 与窗函数 **w[n]** 相乘得到 **x_w[n] = x[n] * w[n]** 时，在截取片段的边界处，信号会被平滑地衰减到 0

这样一来，当你对这个加窗后的信号进行周期延拓时，边界处就变成了连续的 0，消除了原来的突变。“泄漏”因此被显著抑制，代价是原本单根的“谱线”会稍微变宽一点（称为主瓣变宽）

##### 汉宁窗实现

由于加窗后会导致信号峰值幅度下降，FFT的结果需要乘以窗函数增益修正

汉宁窗公式：
$$
w[n] = \frac{1}{2}\left[1 - \cos\left(\frac{2\pi n}{N-1}\right)\right], \quad n = 0,1,\ldots,N-1
$$
加窗公式：
$$
x_w[n] = x[n] \cdot w[n]x_w[n] = x[n] \cdot w[n]
$$
代码实现

```cpp
double applyHanningWindow(double* buffer, int length)
{
    double sum = 0.0;
    for (int i = 0; i < length; i++) {
        double w = 0.5 * (1.0 - std::cos(2.0 * M_PI * i / (length - 1))); // 汉宁窗函数
        buffer[i] *= w;
        sum += w;
    }
    return length / sum; // 窗函数增益
}
```



#### 4. FFT计算

使用PFFFT计算实信号幅度谱

```cpp
PFFFTD_Setup* setup = pffftd_new_setup(length, PFFFT_REAL); // 创建PFFFT设置
double* inputBuffer = (double*)pffftd_aligned_malloc(length * sizeof(double));
double* outputBuffer = (double*)pffftd_aligned_malloc(length * sizeof(double));
double* workBuffer = (double*)pffftd_aligned_malloc(length * sizeof(double));

double mean = 0.0;
for (int i = 0; i < length; i++) {
    mean += buffer[i]; // 计算平均值
}
mean /= length; // 平均值
for (int i = 0; i < length; i++) {
    inputBuffer[i] = buffer[i] - mean; // 去掉直流分量
}

// 应用汉宁窗，减少频谱泄漏，并获取窗函数的相干增益用于补偿
double windowGain = 1.0;
windowGain = applyHanningWindow(inputBuffer, length);

pffftd_transform_ordered(setup, inputBuffer, outputBuffer, workBuffer, PFFFT_FORWARD);

int half = length / 2;
for (int i = 0; i <= half; i++) {
    if (i == 0) {
        double real = outputBuffer[0];
        spectrum[i] = std::fabs(real) / length * windowGain; // DC 不乘2
    } else if (i == half) {
        double realNyq = outputBuffer[1];
        spectrum[i] = std::fabs(realNyq) / length * windowGain; // Nyquist 不乘2
    } else {
        double real = outputBuffer[2 * i];     // 实部
        double imag = outputBuffer[2 * i + 1]; // 虚部
        spectrum[i] = std::sqrt(real * real + imag * imag) * 2.0 / length * windowGain; // 单边幅度并补偿窗函数
    }
}
pffftd_aligned_free(inputBuffer); // 释放内存
pffftd_aligned_free(outputBuffer);
pffftd_aligned_free(workBuffer);
pffftd_destroy_setup(setup); // 销毁PFFFT设置
```

### 完整代码实现

```cpp
#include <iostream>
#include <cmath>
#include "pffft/pffft_double.h"

void generateSineWave(double* buffer, int length, double frequency, double samplerate, double amplitude, double phase)
{
    for (int i = 0; i < length; i++) {
        double t = i / samplerate;                    // 时间（秒）
        double angle = 2.0 * M_PI * frequency * t + phase;  // 角度（弧度）
        buffer[i] = amplitude * std::sin(angle);         // 正弦值
    }
}

// Apply a Hann (Hanning) window in-place and return the coherent gain (mean of window)
double applyHanningWindow(double* buffer, int length)
{
    double sum = 0.0;
    for (int i = 0; i < length; i++) {
        double w = 0.5 * (1.0 - std::cos(2.0 * M_PI * i / (length - 1))); // 汉宁窗函数
        buffer[i] *= w;
        sum += w;
    }
    return length / sum; // 窗函数增益
}

void calculateRealSignalAmplitudeSpectrum(double* buffer, int length, double* spectrum)
{
    PFFFTD_Setup* setup = pffftd_new_setup(length, PFFFT_REAL); // 创建PFFFT设置
    double* inputBuffer = (double*)pffftd_aligned_malloc(length * sizeof(double));
    double* outputBuffer = (double*)pffftd_aligned_malloc(length * sizeof(double));
    double* workBuffer = (double*)pffftd_aligned_malloc(length * sizeof(double));

    double mean = 0.0;
    for (int i = 0; i < length; i++) {
        mean += buffer[i]; // 计算平均值
    }
    mean /= length; // 平均值
    for (int i = 0; i < length; i++) {
        inputBuffer[i] = buffer[i] - mean; // 去掉直流分量
    }

    // 应用汉宁窗，减少频谱泄漏，并获取窗函数的相干增益用于补偿
    double windowGain = 1.0;
    windowGain = applyHanningWindow(inputBuffer, length);

    pffftd_transform_ordered(setup, inputBuffer, outputBuffer, workBuffer, PFFFT_FORWARD);

    int half = length / 2;
    for (int i = 0; i <= half; i++) {
        if (i == 0) {
            double real = outputBuffer[0];
            spectrum[i] = std::fabs(real) / length * windowGain; // DC 不乘2
        } else if (i == half) {
            double realNyq = outputBuffer[1];
            spectrum[i] = std::fabs(realNyq) / length * windowGain; // Nyquist 不乘2
        } else {
            double real = outputBuffer[2 * i];     // 实部
            double imag = outputBuffer[2 * i + 1]; // 虚部
            spectrum[i] = std::sqrt(real * real + imag * imag) * 2.0 / length * windowGain; // 单边幅度并补偿窗函数
        }
    }
    pffftd_aligned_free(inputBuffer); // 释放内存
    pffftd_aligned_free(outputBuffer);
    pffftd_aligned_free(workBuffer);
    pffftd_destroy_setup(setup); // 销毁PFFFT设置
}

int main(int argc, char* argv[]) {
    int length = 1024; // 信号长度（样本数）    
    double samplerate = 8000.0; // 采样率
    double frequency = samplerate / length * 64; // 频率
    double amplitude = 500; // 振幅
    double phase = 0.0; // 初始相位
    double* realSignal = new double[length]; // 分配内存
    double* spectrum = new double[length]; // 分配频谱内存
    generateSineWave(realSignal, length, frequency, samplerate, amplitude, phase); // 生成正弦波
    calculateRealSignalAmplitudeSpectrum(realSignal, length, spectrum); // 计算幅度谱
    double resolution = samplerate / length; // 频率分辨率
    double maxAmplitude = 0.0;
    double maxFreq = 0.0;
    int index = 0;
    int half = length / 2;
    for(int i=0; i < half; i++) {
        double freq = i * resolution; // 计算频率
        if(spectrum[i] >= maxAmplitude) { 
            maxAmplitude = spectrum[i]; // 更新最大幅度
            maxFreq = freq; // 更新最大频率
            index = i; // 更新索引
        }
    }
    std::cout << "Frequency Resolution: " << resolution << " Hz" << std::endl; // 输出频率分辨率
    std::cout << "Peak Frequency: " << frequency << std::endl; // 输出峰值频率和幅度
    std::cout << "Peak Frequency: " << maxFreq << " Hz, Peak Amplitude: " << maxAmplitude << ", Index: " << index << std::endl; // 输出峰值频率和幅度
    return 0;
}
```

### 注意事项

- 奈奎斯特频率为采样率的一半

- 直流分量和奈奎斯特频率的幅度值还原时，不需要除以2
- 对于实信号，能还原的信号频率不超过采样率$fs$的一半


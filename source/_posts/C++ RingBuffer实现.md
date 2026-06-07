---
title: C++ RingBuffer实现
date: 2026-06-07 13:15:01
categories:
    - CMake
tags: 
    - CMake
    - C++
---

C++ RingBuffer实现

<!-- more -->

### 使用场景

C++ RingBuffer, 通过预先申请固定大小的内存，避免读写时重复申请释放内存造成额外开销，同时提高了缓存命中率、

### 代码实现

ring_buffer.h

```cpp
#include <cstdint>
#include <cstdlib>
#include <cstring>

// Small inline copy helpers to avoid memcpy overhead for tiny sizes.
namespace detail {

template<typename T>
static inline void small_copy_to(T* dst, const T* src, size_t n) {
    if(std::is_same_v<T, char>) {
        switch (n) {
            case 0: return;
            case 1: dst[0] = src[0]; return;
            case 2: *(uint16_t*)dst = *(const uint16_t*)src; return;
            case 4: *(uint32_t*)dst = *(const uint32_t*)src; return;
            case 8: *(uint64_t*)dst = *(const uint64_t*)src; return;
            case 16:
                ((uint64_t*)dst)[0] = ((const uint64_t*)src)[0];
                ((uint64_t*)dst)[1] = ((const uint64_t*)src)[1];
                return;
            default: memcpy(dst, src, n); return;
        }
    } else {
        // For non-char types, just use memcpy (or could optimize for small sizes if needed)
        memcpy(dst, src, n * sizeof(T));
    }
}

template<typename T>
static inline void small_copy_from(T* dst, const T* src, size_t n) {
    // same as small_copy_to but clearer semantics
    small_copy_to(dst, src, n);
}

uint32_t next_power_of_two(uint32_t n) {
    if (n == 0) return 1;        // 0 的下一个幂是 1
    if (n > 0x80000000) return 1; // 溢出，无结果
    n -= 1;
    n |= n >> 1;
    n |= n >> 2;
    n |= n >> 4;
    n |= n >> 8;
    n |= n >> 16;
    return n + 1;
}

template<typename T>
class RingBuffer
{
public:
    explicit RingBuffer(uint32_t bufSize) {
        _buffer = nullptr;
        _bufferSize = next_power_of_two(bufSize);
        createBuffer();
    }
    ~RingBuffer() {
        deleteBuffer();
    }
    RingBuffer(const RingBuffer&) = delete;
    RingBuffer& operator=(const RingBuffer&) = delete;

    /**
     * 预览元素
     */
    bool peek(T* data, uint32_t dataSize) {
        if(size()<dataSize) {
            return false;
        }
        uint32_t idx = (_bufferReadCount&_bufferMask);
        if(idx+dataSize<=_bufferSize) {
            detail::small_copy_to(data, _buffer+idx, static_cast<size_t>(dataSize));
        } else {
            uint32_t dataSize1 = _bufferSize-idx;
            uint32_t dataSize2 = dataSize-dataSize1;
            detail::small_copy_to(data, _buffer+idx, static_cast<size_t>(dataSize1));
            detail::small_copy_to(data+dataSize1, _buffer, static_cast<size_t>(dataSize2));
        }
        return true;
    }
    /**
     * 读取元素
     */
    bool read(T* data , uint32_t dataSize) {
        if(size()<dataSize) {
            return false;
        }
        uint32_t idx = (_bufferReadCount&_bufferMask);
        if(idx+dataSize<=_bufferSize) {
            detail::small_copy_to(data, _buffer+idx, static_cast<size_t>(dataSize));
        } else {
            uint32_t dataSize1 = _bufferSize-idx;
            uint32_t dataSize2 = dataSize-dataSize1;
            detail::small_copy_to(data, _buffer+idx, static_cast<size_t>(dataSize1));
            detail::small_copy_to(data+dataSize1, _buffer, static_cast<size_t>(dataSize2));
        }
        _bufferReadCount += dataSize;
        return true;
    }
    /**
     * 写入元素
     */
    bool write(const T* data, uint32_t dataSize) {
        if(capacity()<dataSize) {
            return false;
        }
        uint32_t idx = (_bufferWriteCount&_bufferMask);
        if(idx+dataSize<=_bufferSize) {
            detail::small_copy_from(_buffer+idx, data, static_cast<size_t>(dataSize));
        } else {
            uint32_t dataSize1 = _bufferSize-idx;
            uint32_t dataSize2 = dataSize-dataSize1;
            detail::small_copy_from(_buffer+idx, data, static_cast<size_t>(dataSize1));
            detail::small_copy_from(_buffer, data+dataSize1, static_cast<size_t>(dataSize2));
        }
        _bufferWriteCount += dataSize;
        return true;
    }
    /**
     * 跳过元素
     */
    bool skip(uint32_t skipSize) {
        if(size()<skipSize) {
            return false;
        }
        _bufferReadCount += skipSize;
        return true;
    }
    /**
     * 获取容量
     */
    uint32_t capacity() const {
        return _bufferSize-size();
    }
    /**
     * 获取写入大小
     */
    uint32_t size() const {
        return _bufferWriteCount-_bufferReadCount;
    }
private:
    void createBuffer() {
        _bufferMask = _bufferSize-1;
        _bufferWriteCount = 0;
        _bufferReadCount = 0;
        _buffer = new T[_bufferSize];
    }
    void deleteBuffer() {
        if(_buffer) {
            delete[] _buffer;
        }
    }
private:
    T*          _buffer;
    uint32_t    _bufferSize;
    uint32_t    _bufferMask;
    uint32_t    _bufferWriteCount;
    uint32_t    _bufferReadCount;
}; 

using RingBufferChar = RingBuffer<char>;
using RingBufferInt8= RingBuffer<int8_t>;        
using RingBufferInt16 = RingBuffer<int16_t>;    
} // namespace detail
```

### 使用示例

初始化类型为char的RingBuffer

```cpp
auto ringBuffer = new detail::RingBufferChar(10);
```

### 注意事项

由于其内部通过降维折叠实现，其内部实际bufferSize为2的幂次方

### 降维折叠说明

将线性空间映射到环形空间，通过位运算，可以比取模更快，其中capacity必须为2的幂

```text
index = index & (capacity - 1);
```

### Buffer大小计算说明

因为内部使用无符号计数，溢出后也可以保证其正确性

```text
size = _bufferWriteCount-_bufferReadCount;
```
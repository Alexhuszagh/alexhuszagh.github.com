---
title:  "Scoped Enums and Bitwise Operators"
date:   2018-01-15 14:32:00
categories:
 - c++
 - enum
 - scoped-enums
 - bitwise-operators
tags:
 - c++
---

C++11 introduced the concept of type-safe, scoped enums, which solve both namespace pollution of the neum members into the enclosing scope and implicit conversions to integral types. These enums may be used as follows:

```cpp

enum class ScopedEnum
{
    FIRST = 0,
    SECOND,
    THIRD,
};
```

# Problem

However, scoped enums do not support bitwise operators, preventing their use as flags. For example, I would like to describe an animals and plants using bitwise operators of an scoped enums, but I would like to enforce at compile-time that animals and plants do not mix. As code, this would work out as the following:

```cpp
enum class Animal
{
    CLAWS = 1,
    WINGS = 2,
    ENDANGERED = 4,
};

enum class Plant
{
    AQUATIC = 1,
    CARNIVOROUS = 2,
    ENDANGERED = 4,
};

enum class Canine
{
    FOX = 8,
    WOLF = 16,
    DOG = 32,
};

Animal eagle = Animal::CLAWS | Animal::WINGS | Animal::ENDANGERED;
Plant venus_fly_trap = Plant::CARNIVOROUS;
// Animal hybrid = eagle | venus_fly_trap; compile-time error
Canine wolf = Canine::WOLF | Animal::CLAWS;
// Animal fox = Animal::CLAWS | Canine::FOX; compile-time error
```

# Solution

The following code introduces a single macro, `SCOPED_ENUM_FLAG`, with two possible signatures, that exports type-safe bitwise operators between scoped enums.

To support bitwise operators within a scoped enum, add the following line after the enum declaration:

```cpp
SCOPED_ENUM_FLAG(Animal)
```

To support bitwise operations between two different scoped enums, where the returned enum from each bitwise operator is the left-most type (in this case, `Canine`), add the following line after the enum declaration:

```cpp
SCOPED_ENUM_FLAG(Canine, Animal)
```

# Code

```cpp
// This is free and unencumbered software released into the public domain.
// 
// Anyone is free to copy, modify, publish, use, compile, sell, or 
// distribute this software, either in source code form or as a compiled 
// binary, for any purpose, commercial or non-commercial, and by any 
// means.
// 
// In jurisdictions that recognize copyright laws, the author or authors 
// of this software dedicate any and all copyright interest in the 
// software to the public domain. We make this dedication for the 
// benefit of the public at large and to the detriment of our heirs and 
// successors. We intend this dedication to be an overt act of 
// relinquishment in perpetuity of all present and future rights to 
// this software under copyright law.
// 
// THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, 
// EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF 
// MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. 
// IN NO EVENT SHALL THE AUTHORS BE LIABLE FOR ANY CLAIM, DAMAGES OR 
// OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, 
// ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
// OTHER DEALINGS IN THE SOFTWARE.
// 
// For more information, please refer to <http://unlicense.org/>

#include <type_traits>
#include <cstdint>

// HELPERS
// -------

/**
 *  \brief Get enum underlying type.
 */
template <typename T>
inline typename std::underlying_type<T>::type enumc_to_int(T t)
{
    return static_cast<typename std::underlying_type<T>::type>(t);
}

// MACROS
// ------

/**
 *  \brief Macro to define enum operators between enumerations.
 *
 *  Supports `&`, `&=`, `|`, `|=`, `^`, `^=`, `~`, and bool conversion.
 */
#define SCOPED_ENUM_FLAG2(lhs_t, ths_t)                                     \
    /*  \brief Bitwise or operator. */                                      \
    inline lhs_t operator|(lhs_t lhs, ths_t rhs) noexcept                   \
    {                                                                       \
        return static_cast<lhs_t>(enumc_to_int(lhs) | enumc_to_int(rhs));   \
    }                                                                       \
                                                                            \
    /*  \brief Bitwise or assignment operator. */                           \
    inline lhs_t & operator|=(lhs_t &lhs, ths_t rhs) noexcept               \
    {                                                                       \
        lhs = static_cast<lhs_t>(enumc_to_int(lhs) | enumc_to_int(rhs));    \
        return lhs;                                                         \
    }                                                                       \
                                                                            \
    /*      \brief Bitwise and operator. */                                 \
    inline lhs_t operator&(lhs_t lhs, ths_t rhs) noexcept                   \
    {                                                                       \
        return static_cast<lhs_t>(enumc_to_int(lhs) & enumc_to_int(rhs));   \
    }                                                                       \
                                                                            \
    /*  \brief Bitwise and assignment operator. */                          \
    inline lhs_t & operator&=(lhs_t &lhs, ths_t rhs) noexcept               \
    {                                                                       \
        lhs = static_cast<lhs_t>(enumc_to_int(lhs) & enumc_to_int(rhs));    \
        return lhs;                                                         \
    }                                                                       \
                                                                            \
    /*  \brief Bitwise xor operator. */                                     \
    inline lhs_t operator^(lhs_t lhs, ths_t rhs) noexcept                   \
    {                                                                       \
        return static_cast<lhs_t>(enumc_to_int(lhs) ^ enumc_to_int(rhs));   \
    }                                                                       \
                                                                            \
    /*  \brief Bitwise xor assignment operator. */                          \
    inline lhs_t & operator^=(lhs_t &lhs, ths_t rhs) noexcept               \
    {                                                                       \
        lhs = static_cast<lhs_t>(enumc_to_int(lhs) ^ enumc_to_int(rhs));    \
        return lhs;                                                         \
    }


/**
 *  \brief Set enumeration flags within the same enum.
 */
#define SCOPED_ENUM_FLAG1(enum_t)                                           \
    SCOPED_ENUM_FLAG2(enum_t, enum_t)                                       \
                                                                            \
    /*  \brief Bitwise negation operator. */                                \
    inline enum_t operator~(enum_t value) noexcept                          \
    {                                                                       \
        return static_cast<enum_t>(~enumc_to_int(value));                   \
    }                                                                       \
                                                                            \
    /*  \brief Negation operator. */                                        \
    inline bool operator!(enum_t value) noexcept                            \
    {                                                                       \
        return enumc_to_int(value) == 0;                                    \
    }

/**
 *  \brief Macros to grab the proper bit-wise flag setter.
 *  `SCOPED_ENUM_ID` is required for MSVC compatibility, since MSVC
 *  has issues in expanding `__VA_ARGS__` for the dispatcher.
 *  Don't remove it, even if the above code works without it 
 *  for GCC and Clang.
 */
#define SCOPED_ENUM_ID(x) x
#define GET_SCOPED_ENUM_FLAG(_1,_2,NAME,...) NAME
#define SCOPED_ENUM_FLAG(...) SCOPED_ENUM_ID(GET_SCOPED_ENUM_FLAG(__VA_ARGS__, SCOPED_ENUM_FLAG2, SCOPED_ENUM_FLAG1)(__VA_ARGS__))
```

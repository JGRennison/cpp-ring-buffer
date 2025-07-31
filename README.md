## C++ self-resizing ring buffer container

This provides functionality and an interface similar to `std::deque`.
Elements can be inserted and removed at both the front and back with (amortised) constant time.

Erases at the front and back do not invalidate iterators or references.

Erases and inserts in the middle of the container are O(N) in the distance to the closest end, and invalidate existing iterators and references.

Inserts when the container is full (new size >= capacity) reallocate the container with a larger capacity, move existing elements to the new backing storage,
and invalidate existing iterators and references; this behaviour is similar to `std::vector`.
This is different to fixed-sized ring buffers where an element at the other end would be overriden.

The capacity of the container is always a power of 2 (the minimum for a non-empty container is 4).
The backing storage is a single contiguous piece of memory, this reduces overhead and memory use.

The code targets C++20. Earlier C++ versions may require modifications.

The container supports custom allocators in the second template parameter (the default is `std::allocator<T>`).

This container can be used with container adapters such as `std::queue` and `std::stack`.

### Usage

Include `ring_buffer.hpp` and use `jgr::ring_buffer<T>` or `jgr::ring_buffer<T, Allocator>`.

### Use case

Small, low-overhead queues of movable elements.

### Limitations

This is not a concurrent queue. It is not safe for multiple threads to access the same instance of the ring buffer without suitable locking.

Ring buffers are limited to a maximum size and capacity of `1 << 31` (about 2.1 billion), `std::length_error` is thrown if this is exceeded.

Unlike `std::deque` (and like `std::vector`) inserting elements can cause the backing storage to be reallocated, invalidating existing references and iterators.

`push_back`, `push_front`, `emplace_front` and `emplace_back` may reference an existing element in the container, even in the case where the container will be
reallocated to insert the new element. i.e. `ring.emplace_back(std::move(ring.back())` is allowed for a non-empty container.
This is not the case for `insert` and `emplace`. `ring.emplace(ring.begin() + N, std::move(ring.back()))` is undefined behaviour.

If inserting, moving, copying or otherwise constructing an element throws an exception, the result is undefined.

### Tests

Building the tests requires CMake. The Catch test framework is included in `3rdparty/catch2`.

To run the tests:
```
mkdir build
cd build
cmake ../test
make
ctest
```

To run the tests with sanitisers enabled, add `-DOPTION_SANITISERS=ON` to the cmake command.

### URLs

This project is hosted at https://github.com/JGRennison/cpp-ring-buffer

### License

MIT License, see LICENSE file.

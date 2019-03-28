#include <cassert>
#include <iostream>
#include <vector>
#include <string>
#include <utility>

template<typename T>
class VectorInternalsAccessor;

template<typename T>
class MyVector {
 public:
  MyVector();
  MyVector(size_t size, T value = T());
  ~MyVector();

  MyVector(const MyVector& other);
  MyVector& operator=(const MyVector& other);

  MyVector(MyVector&& other) noexcept;
  MyVector& operator=(MyVector&& other) noexcept;

  size_t Size() const;

  bool IsEmpty() const;

  void PushBack(const T& value);

  void PopBack();

  void PushFront(const T& value);

  void PopFront();

  T& operator[](size_t index);
  const T& operator[](size_t index) const;

  template<typename ... Args>
  void EmplaceBack(Args&& ... args);

  template<typename ... Args>
  void EmplaceFront(Args&& ... args);

  int Find(const T& value) const;

 protected:
  size_t size_;
  size_t allocated_size_;
  T* data_;
  void Relocate(size_t new_size);

  friend class VectorInternalsAccessor<T>;
};

template<typename T>
class VectorInternalsAccessor {
 public:
  VectorInternalsAccessor() = delete;
  static size_t Size(const MyVector<T>& vector) {
    return vector.size_;
  }
  static T* AllocData(const MyVector<T>& vector) {
    return vector.data_;
  }
  static size_t AllocSize(const MyVector<T>& vector) {
    return vector.allocated_size_;
  }
};

template<typename T>
MyVector<T>::MyVector() : size_(0),
                          allocated_size_(1),
                          data_(new T[1]) {}

template<typename T>
MyVector<T>::MyVector(size_t size, T value) : size_(size),
                                              allocated_size_(size * 2 + 1) {
  data_ = new T[allocated_size_];
  for (size_t i = 0; i < size_; ++i) {
    data_[i] = value;
  }
}

template<typename T>
MyVector<T>::~MyVector() {
  delete[] data_;
}

template<typename T>
MyVector<T>::MyVector(const MyVector& other)
    : size_(other.size_),
      allocated_size_(other.allocated_size_),
      data_(new T[allocated_size_]) {
  for (size_t i = 0; i < size_; i++) {
    data_[i] = other.data_[i];
  }
}

template<typename T>
MyVector<T>& MyVector<T>::operator=(const MyVector& other) {
  if (allocated_size_ != other.allocated_size_) {
    allocated_size_ = other.allocated_size_;
    delete[] data_;
    data_ = new T[allocated_size_];
  }
  size_ = other.size_;
  for (size_t i = 0; i < size_; i++) {
    data_[i] = other.data_[i];
  }
  return *this;
}

template<typename T>
MyVector<T>::MyVector(MyVector&& other) noexcept
    : size_(other.size_),
      allocated_size_(other.allocated_size_),
      data_(other.data_) {
  other.size_ = 0;
  other.allocated_size_ = 1;
  other.data_ = new T[1];
}

template<typename T>
MyVector<T>& MyVector<T>::operator=(MyVector&& other) noexcept {
  delete[] data_;
  size_ = other.size_;
  allocated_size_ = other.allocated_size_;
  data_ = other.data_;
  other.size_ = 0;
  other.allocated_size_ = 1;
  other.data_ = new T[1];
  return *this;
}

template<typename T>
size_t MyVector<T>::Size() const {
  return size_;
}

template<typename T>
bool MyVector<T>::IsEmpty() const {
  return size_ == 0;
}

template<typename T>
void MyVector<T>::PushBack(const T& value) {
  if (size_ == allocated_size_) {
    Relocate(size_ * 2);
  }
  data_[size_++] = value;
}

template<typename T>
void MyVector<T>::PopBack() {
  assert(size_ != 0);
  size_--;
  if (size_ < allocated_size_ / 4) {
    Relocate(allocated_size_ / 2);
  }
}

template<typename T>
void MyVector<T>::PushFront(const T& value) {
  if (size_ == allocated_size_) {
    Relocate(size_ * 2);
  }
  size_++;
  for (size_t i = size_; i > 1; i--) {
    data_[i - 1] = data_[i - 2];
  }
  data_[0] = value;
}

template<typename T>
void MyVector<T>::PopFront() {
  assert(size_ != 0);
  for (size_t i = 0; i < size_ - 1; ++i) {
    data_[i] = data_[i + 1];
  }
  size_--;
  if (size_ < allocated_size_ / 4) {
    Relocate(allocated_size_ / 2);
  }
}

template<typename T>
T const& MyVector<T>::operator[](size_t index) const {
  assert(index < size_);
  return data_[index];
}

template<typename T>
T& MyVector<T>::operator[](size_t index) {
  assert(index < size_);
  return data_[index];
}

template<typename T>
template<typename ... Args>
void MyVector<T>::EmplaceBack(Args&& ... args) {
  if (size_ == allocated_size_) {
    Relocate(size_ * 2);
  }
  data_[size_++] = T(std::forward<Args>(args) ...);
}

template<typename T>
template<typename ... Args>
void MyVector<T>::EmplaceFront(Args&& ... args) {
  if (size_ == allocated_size_) {
    Relocate(size_ * 2);
  }
  size_++;
  for (size_t i = size_; i > 1; i--) {
    data_[i - 1] = data_[i - 2];
  }
  data_[0] = T(std::forward<Args>(args) ...);
}

template<typename T>
int MyVector<T>::Find(const T& value) const {
  for (size_t i = 0; i < size_; i++) {
    if (data_[i] == value) {
      return i;
    }
  }
  return -1;
}

template<typename T>
void MyVector<T>::Relocate(size_t new_size) {
  allocated_size_ = new_size;
  T* temp = data_;
  data_ = new T[allocated_size_];
  for (size_t i = 0; i < size_; i++) {
    data_[i] = temp[i];
  }
  delete[] temp;
}

// Для тестирования группы закомментируйте или удалите строчку
// "#define SKIP_XXXXX" для соответствующей группы тестов.
//
// Подзадачи:
//    (1) Базовая функциональность и операции с хвостом вектора:
//          А, Б, В, Г, Д1, Д2, Е, Ж, З, И
// #define SKIP_BASIC
//    (2) Релоцирование памяти - К
// #define SKIP_RELOC
//    (3) : Работа с началом вектора - М
// #define SKIP_XFRONT
//    (4) : Find - О
// #define SKIP_FIND
//    (5) : Emplace - П
// #define SKIP_EMPLACE
//    (6) Копирование - Л
// #define SKIP_COPY
//    (7) : Перемещение - Н
// #define SKIP_MOVE

#ifndef IGNORE_MAIN
int main() {
#ifndef SKIP_BASIC
  {
    MyVector<int> v;
    const MyVector<int> &const_v = v;

    assert(const_v.Size() == 0 && const_v.IsEmpty());

    v.PushBack(42);
    assert(const_v.Size() == 1 && !const_v.IsEmpty());
    assert(v[0] == 42);
    assert(const_v[0] == 42);

    v[0] *= 10101;

    assert(v[0] == 424242);
    assert(const_v[0] == 424242);

    v.PopBack();
    assert(const_v.Size() == 0 && const_v.IsEmpty());
  }
  std::cout << "[PASS] Basic" << std::endl;
#else
  std::cout << "[SKIPPED] Basic" << std::endl;
#endif  // SKIP_BASIC

#ifndef SKIP_RELOC
  {
    MyVector<int> v;
    assert(VectorInternalsAccessor<int>::AllocSize(v) == 1);
    for (int i = 0; i < 1025; ++i) {
      v.PushBack(i);
    }
    assert(VectorInternalsAccessor<int>::AllocSize(v) == 2048);
    for (int i = 0; i < 1025; ++i) {
      assert(v[i] == i);
    }
  }
  std::cout << "[PASS] Relocation" << std::endl;
#else
  std::cout << "[SKIPPED] Relocation" << std::endl;
#endif  // SKIP_RELOC

#ifndef SKIP_XFRONT
  {
    MyVector<int> v;

    v.PushBack(42);
    assert(v.Size() == 1 && !v.IsEmpty());
    assert(v[0] == 42);

    v.PushFront(1);
    assert(v.Size() == 2 && !v.IsEmpty());
    assert(v[0] == 1);
    assert(v[1] == 42);

    v.PopFront();
    assert(v.Size() == 1 && !v.IsEmpty());
    assert(v[0] == 42);

    v.PopBack();
    assert(v.Size() == 0 && v.IsEmpty());

    v.PushFront(777);
    assert(v.Size() == 1 && !v.IsEmpty());
    assert(v[0] == 777);

    v.PopFront();
    assert(v.Size() == 0 && v.IsEmpty());
  }
  std::cout << "[PASS] XFront" << std::endl;
#else
  std::cout << "[SKIPPED] XFront" << std::endl;
#endif  // SKIP_XFRONT

#ifndef SKIP_FIND
  {
    MyVector<int> v;
    v.PushBack(0);
    v.PushBack(1);
    v.PushBack(2);
    v.PushBack(1);
    const auto &const_v = v;

    assert(const_v.Find(0) == 0);
    assert(const_v.Find(1) == 1);
    assert(const_v.Find(2) == 2);
    assert(const_v.Find(3) == -1);
  }
  std::cout << "[PASS] Find" << std::endl;
#else
  std::cout << "[SKIPPED] Find" << std::endl;
#endif  // SKIP_FIND

#ifndef SKIP_EMPLACE
  {
    struct MyStruct {
      int i_field;
      std::string s_field;
      MyStruct() : i_field(0), s_field("<EMPTY>") {}
      MyStruct(int i_field, std::string s_field)
          : i_field(i_field), s_field(std::move(s_field)) {}
    };
    MyVector<MyStruct> v;

    v.EmplaceBack(MyStruct());
    v.EmplaceBack();
    v.EmplaceBack(42, "4242");
    v.EmplaceFront(MyStruct());
    v.EmplaceFront();
    v.EmplaceFront(1111, "2222");

    assert(v[0].i_field == 1111 && v[0].s_field == "2222");
    assert(v[1].i_field == 0 && v[1].s_field == "<EMPTY>");
    assert(v[2].i_field == 0 && v[2].s_field == "<EMPTY>");
    assert(v[3].i_field == 0 && v[3].s_field == "<EMPTY>");
    assert(v[4].i_field == 0 && v[4].s_field == "<EMPTY>");
    assert(v[5].i_field == 42 && v[5].s_field == "4242");
  }
  std::cout << "[PASS] Empalce" << std::endl;
#else
  std::cout << "[SKIPPED] Empalce" << std::endl;
#endif  // SKIP_EMPLACE

#ifndef SKIP_COPY
  {
    MyVector<int> v;
    for (int i = 0; i < 67; ++i) {
      v.PushBack(i);
    }

    MyVector<int> other(v);
    for (int i = 0; i < 67; ++i) {
      assert(v[i] == i);
      assert(other[i] == i);
    }

    v[0] = 1111;
    other[1] = 2222;

    assert(v[0] == 1111);
    assert(v[1] == 1);
    assert(other[0] == 0);
    assert(other[1] == 2222);

    for (int i = 2; i < 67; ++i) {
      assert(v[i] == i);
      assert(other[i] == i);
    }
  }
  {
    MyVector<int> v;
    for (int i = 0; i < 67; ++i) {
      v.PushBack(i);
    }

    MyVector<int> other = v;
    for (int i = 0; i < 67; ++i) {
      assert(v[i] == i);
      assert(other[i] == i);
    }

    v[0] = 1111;
    other[1] = 2222;

    assert(v[0] == 1111);
    assert(v[1] == 1);
    assert(other[0] == 0);
    assert(other[1] == 2222);

    for (int i = 2; i < 67; ++i) {
      assert(v[i] == i);
      assert(other[i] == i);
    }
  }
  {
    MyVector<int> v;
    for (int i = 0; i < 67; ++i) {
      v.PushBack(i);
    }

    MyVector<int> other;
    for (int i = 0; i < 6; ++i) {
      v.PushBack(i * 55);
    }

    other = v;
    for (int i = 0; i < 67; ++i) {
      assert(v[i] == i);
      assert(other[i] == i);
    }

    v[0] = 1111;
    other[1] = 2222;

    assert(v[0] == 1111);
    assert(v[1] == 1);
    assert(other[0] == 0);
    assert(other[1] == 2222);

    for (int i = 2; i < 67; ++i) {
      assert(v[i] == i);
      assert(other[i] == i);
    }
  }
  std::cout << "[PASS] Copy" << std::endl;
#else
  std::cout << "[SKIPPED] Copy" << std::endl;
#endif  // SKIP_COPY

#ifndef SKIP_MOVE
  {
    MyVector<int> v;
    v.PushBack(0);
    v.PushBack(1);
    v.PushBack(2);
    const int *const v_data = VectorInternalsAccessor<int>::AllocData(v);

    MyVector<int> other(std::move(v));

    assert(other.Size() == 3);
    assert(VectorInternalsAccessor<int>::AllocData(other) == v_data);
    assert(VectorInternalsAccessor<int>::AllocSize(other) == 4);

    assert(v.Size() == 0);
    assert(VectorInternalsAccessor<int>::AllocData(v)
               != VectorInternalsAccessor<int>::AllocData(other));
    assert(VectorInternalsAccessor<int>::AllocSize(v) == 1);
  }
  {
    MyVector<int> v;
    v.PushBack(0);
    v.PushBack(1);
    v.PushBack(2);
    const int *const v_data = VectorInternalsAccessor<int>::AllocData(v);

    MyVector<int> other;
    const int *const other_intitial_data =
        VectorInternalsAccessor<int>::AllocData(v);

    other = std::move(v);

    assert(other.Size() == 3);
    assert(VectorInternalsAccessor<int>::AllocData(other) == v_data);
    assert(VectorInternalsAccessor<int>::AllocSize(other) == 4);

    assert(v.Size() == 0);
    assert(VectorInternalsAccessor<int>::AllocData(v)
               != VectorInternalsAccessor<int>::AllocData(other));
    assert(VectorInternalsAccessor<int>::AllocData(v) != other_intitial_data);
    assert(VectorInternalsAccessor<int>::AllocSize(v) == 1);
  }
  std::cout << "[PASS] Move" << std::endl;
#else
  std::cout << "[SKIPPED] Move" << std::endl;
#endif  // SKIP_MOVE

  std::cout << "Finished!" << std::endl;
  return 0;
}
#endif

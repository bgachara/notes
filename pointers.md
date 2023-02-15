# Pointers in C

`Notes from the book with the same name and on the same topic`

## Introduction

- This is a variable that stores the address of a memory location.
- Can contain the address in memory of another variable, object or function.
- Object is said to be memory allocated using one of the memory allocation functions such as malloc.
- Gets its type from what it points to: such as pointer to a char.
- Nothing inherent in the pointer indicated what type data pointer is referencing. Only contains address.
- Key to comprehend pointers is understanding how memory is managed in a C program.
- When a C program is compiled it works with 3 types of memory all differentiated by their scope and lifetime:
  - Static/Global - static vars and global vars use this.
  - Automatic - declared within a function.
  - Dynamic - allocated from the heap and released when necessary.
- Most pointers are used to manipulate data in memory, hence need to understand how memory is partitioned and organised.
- Faster and more efficient code can be written because pointers are closer to the hardware, compiler can easily translate operation into machine code.
- Many data structures are more easily implemented using pointers. i.e linked list, arrays.

### Uses of pointers

- Creating fast and efficient code.
- Convenient means for addressing many types of problems.
- Support dynamic memory allocation
- Make expression compact and succinct.
- Reduce overhead by passing data structures by pointers.
- Protecting data passed as a parameter to a function.
- Multiple levels of indirection. i.e double pointer,
- Constant pointers

### Pointer hazards

- Accessing arrays and other data structures beyond their bounds.
- Referencing automatic variables after they have gone out of existence.
- Referencing heap allocated memory after it has been released.
- Dereferencing a pointer before memory has been allocated to it.

### Declaring pointers

```c
 int *num
 int* num

 const int *pci

// overloaded symbol as its also used for multiplication.

// The trick is to read the declaration backward.

// operator address

  num = 0;
  pi = &num;

// assignment of integer to pointer will cause a warning.
// good practice to initialize a pointer as soon as possible.

// indirection operator * is used to return the value pointed to by a pointer variable. deferencing
  int num = 0;
  int *pi = &num;
  
  printf("%p\n", *pi);
  
// use the result of dereference as lvalue hence assignable.

```
 
- An invalid address is one that the program is not authorized to access.
- Uninitialized memory refers to references to variables uninitialized thus contain garbage.
- Virtual memory and pointers.
- The size of a pointer depends on the machine in use and compiler.
- On modern platforms, the size of a pointer is the same regardless of pointer type.
- Predefined pointer data types.
- Pointer operators {*, *, ->, +, -, ==!=, >>=<<=}
- Pointer Arithmetic
  - Add an integer to a pointer.
  - Subtracting an integer from a pointer.
  - Subtracting two pointer from each other.(only useful when need to know the order of elements in an array)
  - Comparing pointers.
  

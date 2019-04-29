## block



```c
// 无参数无返回值的Block分析
int main(int argc, const char * argv[]) {
    void (^blk) (void) = ^{
        printf("block invoke");
    };
    blk();
    return 0;
}
```

block的结构体包含两个属性

```c
struct __block_impl impl;//函数地址
struct __main_block_desc_0* Desc;//描述
```

**使用自动变量**

```c
int main(int argc, const char * argv[]) {
    int age = 100;
    const char *name = "zyt";
    void (^blk) (void) = ^{
        printf("block invoke age = %d age = %s", age, name);
    };
    age = 101;
    blk();
    return 0;
}
```

block结构体多了两个参数

```c
struct __block_impl impl;//函数地址
struct __main_block_desc_0* Desc;//描述
int age;
const char *name;
```

相当于复制了外部变量的值，类似于函数的实参传形参

**使用__block修饰的变量**

```c
int main(int argc, const char * argv[]) {
    __block int age = 100;
    const char *name = "zyt";
    void (^blk) (void) = ^{
        printf("block invoke age = %d age = %s", age, name);
    };
    age = 101;
    blk();
    return 0;
}
```

```c
struct __block_impl impl;//函数地址
struct __main_block_desc_0* Desc;//描述
__Block_byref_age_0 *age;
const char *name;
```

**Block的强引用**


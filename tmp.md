

确定自己终端在`/gray-scott-sim` 路径下

```bash
mkdir build && cd build
cmake ..
cmake --build .
ctest
```



test前可以删去

build文件夹

```shell
rm -rf build
```

## eg. for git commit

比如完成了test_unit_1.cpp: 

<u>title</u>: feat: Complete test_unit_1.cpp 

<u>description</u>:  Check that the type of the model parameters (F, k) matches that of the element type of the u and v vectors.


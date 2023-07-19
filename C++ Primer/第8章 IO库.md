# 第8章 IO库
## IO类
```
头文件iostream
类(w)istream读,(w)ostream写,(w)iostream读写

头文件fstream
类(w)ifstream读,(w)ofstream写,(w)fstream读写

头文件sstream
类(w)istringstream读,(w)ostringstream写,(w)stringstream读写
```
fstream和sstream继承自iostream
```
string s1, s2;
getline(cin, s1);            //使用getline读取标准输入
ifstream FILE("hello.txt");
getline(FILE, s2);           //使用getline读取文件

cout << "Hello!" << endl;    //写入到标准输出
ofstream of("hello.txt");
of << "This is a sentence."; //写入到输出文件流

//iostream类的特性同样适用于fstream和sstream
```
IO对象无拷贝或赋值
```
ofstream out1, out2;
out1 = out2;         //错误：不能对流对象赋值

ofstream print(ofstream);
out1 = print(out2); //错误：不能拷贝流对象

//进行IO操作的函数通常以引用方式传递和返回流
ofstream &hello(ofstream &out){
    out << "Hello!" << endl;
    return out;
}

//读写一个IO对象会改变其状态，因此传递和返回的引用不能为const
```
条件状态
```
while(cin >> word){ ... }  //若流保持有效状态，条件为真

cin.rdstate()              //调用rdstate方法查询流的状态
iostate值：
    0：goodbit 无错误
    1：badbit I/O操作上的读/写错误
    2：eofbit 输入操作达到文件结尾
    4：failbit I/O操作出现逻辑错误
```
管理输出缓冲
```
每个输出流管理一个缓冲区，缓冲刷新时，数据才被写到输出设备或文件   
导致缓冲刷新的原因：
    1. 程序正常结束
    2. 缓冲区满
    3. 使用操作符（如endl）显式刷新缓冲
        cout << endl;         //换行并刷新
        cout << ends;         //输出一个空字符并刷新
        cout << flush;        //刷新，不附加额外字符
    4. 流的内部状态被设置为立即刷新
        cout << unitbuf;      //将标准输出设置为立即刷新
    5. 输出流被关联到另一个流
        //cin/cerr默认关联到cout，因此读cin或写cerr都会刷新cout
```
## 文件输入输出
创建文件流对象
```
ifstream in;
in.open(FILE);          //先声明后打开文件
ofstream out(FILE);     //构造文件流并打开文件
if(out){...}            //检查是否成功打开文件
```
关闭文件
```
in.close()              //显式关闭文件
for(auto p = argv + 1; p != argv + argc ; ++p){
    ifstream input(*p);
    ...
}                       //当文件流对象被销毁时，close会被自动调用
```
文件模式
```
in           //以读方式打开
out          //以写方式打开
app          //每次写操作均定位到文件末尾
ate          //每次打开后立即定位到文件末尾
trunc        //截断文件
binary       //以二进制方式进行IO

//ofstream打开文件会默认截断文件，想要保留文件内容并在末尾输出，须在打开时指定文件模式为app
ofstream app("file.txt", ofstream::app);
```
## string流
根据空格断开字符串
```
string line, word;
vector<string> strs;
getline(cin,line);            //用getline读取一行带空格的字符串
istringstream is(line);       //定义字符串流，绑定line字符串
while(is >> word)
    strs.push_back(word);     //根据空格每次读取一个单词
```
格式化输出
```
ostringstream os;
while(const auto &num : nums)
    os << num;          //用标准输出运算符向字符串流添加数据
cout << os.str();       //用str()方法输出字符串流关联的字符串
```
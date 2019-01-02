# 编译原理

## 阶段一

对原PL0编译程序的修改

1. 将中文字符变成英文字符使得程序可编译运行。
    如把符号非换成 not， 因为goto 会导致编译错误，所以将goto的地方变为exit，结束程序

2. ≠ 和 ≤ 以及 ≥ 无法使用，所以用<>代替不等于，<=代替小于等于， >= 代替大于等于。
    然后在getsym语法分析中加入对这三个符号的识别，在 < 和 > 的识别上加入分支
    ```pascal
    ······
    else if ch = '<' Then
    Begin
            getch;
            If ch = '=' Then
            Begin
                sym := leq;
                getch
            End
            Else If ch = '>' Then 
            Begin
                sym := neq;
                getch
            End
            Else sym := lss
    End
    Else If ch = '>' Then 
    Begin
            getch;
            If ch = '=' Then
            Begin
                sym := geq;
                getch
            End
            Else sym := gtr;
        End
    Else
        Begin
        sym := ssym[ch];
        getch
        End
    End {getsym};
    ```
3. 加入文件的输入输出操作。
    将命令行参数赋值给输入输出的文件变量
    ```pascal
    assign (file_input,paramstr(1));
    assign (file_output,paramstr(2));
    reset(file_input);
    rewrite(file_output);
    ```

    将所有标准输入输出改为文件输入输出
    ```pascal
    read(file_input,ch);
    write(file_output,ch);
    ```

中间代码:

```pascal
    0 const  m = 7, n = 85;
    1 var  x, y, z, q, r;
    1 procedure  multiply;
    1   var  a, b;
    2   begin  a := x;  b := y;  z := 0;
    9     while b > 0 do
   13       begin  
   13         if odd b then z := z + a; 
   20         a := 2*a ;  b := b/2 ;
   28       end
   28   end;
    2  INT    0    5
    3  LOD    1    3
    4  STO    0    3
    5  LOD    1    4
    6  STO    0    4
    7  LIT    0    0
    8  STO    1    5
    9  LOD    0    4
   10  LIT    0    0
   11  OPR    0   12
   12  JPC    0   29
   13  LOD    0    4
   14  OPR    0    6
   15  JPC    0   20
   16  LOD    1    5
   17  LOD    0    3
   18  OPR    0    2
   19  STO    1    5
   20  LIT    0    2
   21  LOD    0    3
   22  OPR    0    4
   23  STO    0    3
   24  LOD    0    4
   25  LIT    0    2
   26  OPR    0    5
   27  STO    0    4
   28  JMP    0    9
   29  OPR    0    0
   30 procedure  divide;
   30   var  w;
   31   begin  r := x;  q := 0;  w := y;
   38     while w <= r do w := 2*w ;
   47     while w > y do
   51     begin  q := 2*q;  w := w/2;
   59       if w <= r then
   62       begin  r := r-w;  q := q+1 end
   71     end
   71   end;
   31  INT    0    4
   32  LOD    1    3
   33  STO    1    7
   34  LIT    0    0
   35  STO    1    6
   36  LOD    1    4
   37  STO    0    3
   38  LOD    0    3
   39  LOD    1    7
   40  OPR    0   13
   41  JPC    0   47
   42  LIT    0    2
   43  LOD    0    3
   44  OPR    0    4
   45  STO    0    3
   46  JMP    0   38
   47  LOD    0    3
   48  LOD    1    4
   49  OPR    0   12
   50  JPC    0   72
   51  LIT    0    2
   52  LOD    1    6
   53  OPR    0    4
   54  STO    1    6
   55  LOD    0    3
   56  LIT    0    2
   57  OPR    0    5
   58  STO    0    3
   59  LOD    0    3
   60  LOD    1    7
   61  OPR    0   13
   62  JPC    0   71
   63  LOD    1    7
   64  LOD    0    3
   65  OPR    0    3
   66  STO    1    7
   67  LOD    1    6
   68  LIT    0    1
   69  OPR    0    2
   70  STO    1    6
   71  JMP    0   47
   72  OPR    0    0
   73 procedure  gcd;
   73   var  f, g ;
   74   begin  f := x;  g := y;
   79     while f <> g do
   83     begin
   83       if f < g then g := g-f;
   91       if g < f then f := f-g;
   99     end;
  100     z := f
  101   end;
   74  INT    0    5
   75  LOD    1    3
   76  STO    0    3
   77  LOD    1    4
   78  STO    0    4
   79  LOD    0    3
   80  LOD    0    4
   81  OPR    0    9
   82  JPC    0  100
   83  LOD    0    3
   84  LOD    0    4
   85  OPR    0   10
   86  JPC    0   91
   87  LOD    0    4
   88  LOD    0    3
   89  OPR    0    3
   90  STO    0    4
   91  LOD    0    4
   92  LOD    0    3
   93  OPR    0   10
   94  JPC    0   99
   95  LOD    0    3
   96  LOD    0    4
   97  OPR    0    3
   98  STO    0    3
   99  JMP    0   79
  100  LOD    0    3
  101  STO    1    5
  102  OPR    0    0
  103 begin 
  104   x := m;  y := n;  call multiply;
  109   x := 25;  y:= 3;  call divide;
  114   x := 84;  y := 36;  call gcd;
  119 end.
  103  INT    0    8
  104  LIT    0    7
  105  STO    0    3
  106  LIT    0   85
  107  STO    0    4
  108  CAL    0    2
  109  LIT    0   25
  110  STO    0    3
  111  LIT    0    3
  112  STO    0    4
  113  CAL    0   31
  114  LIT    0   84
  115  STO    0    3
  116  LIT    0   36
  117  STO    0    4
  118  CAL    0   74
  119  OPR    0    0
```

栈中数据

```pascal
START PL/0
7
85
7
85
0
7
14
42
28
21
35
56
10
112
5
147
224
2
448
1
595
896
0
25
3
25
0
3
6
12
24
48
0
24
1
1
2
12
4
6
8
3
84
36
84
36
48
12
24
12
12
END PL/0
```

## 阶段二

在PL0语言中增加Read和Write语句;

1. 添加保留字的数目，从11个增加到13个。
    ```pascal
    norw = 13; {保留字的个数}
    ```
   因为保留字长度定为10，所以添加read和write要在后面补空格。

2. 将read和write添加到symbol符号定义
    ```pascal
    symbol = (nul, ident, number, plus, minus, times, slash, oddsym,
            eql, neq, lss, leq, gtr, geq, lparen, rparen, comma, semicolon,
            period, becomes, beginsym, endsym, ifsym, thensym,
            whilesym, dosym, callsym, constsym, varsym, procsym, readsym, writesym );
    ······
    wsym[9] := readsym;   wsym[10] := thensym;
    wsym[11] := varsym;   wsym[12] := whilesym;
    wsym[13] := writesym;
    ```

3. 使语句能从read和write开始，增加中间代码RED和WRT。
    ```pascal
    statbegsys := [beginsym, callsym, ifsym, whilesym, writesym, readsym];
    ······
    mnemonic[red] := 'RED  ';mnemonic[wrt] := 'WRT  ';
    ```

4. 在语法分析器中，对保留字的识别加入read和write，写在后面。
    ```pascal
    ······
    Else
        If sym = readsym Then
        Begin
            getsym;
            if sym = lparen Then
            Begin
                Repeat
                getsym;
                If sym = ident Then
                    Begin
                    i := position(id);
                    If(i = 0) Then
                        error(11)
                    Else If table[i].kind <> variable Then
                        Begin
                        error(12);
                        i := 0
                        end
                    Else with table[i] do
                        gen(red,lev-level,adr)
                    end
                    else error(4);
                    getsym;
                until sym <> comma
            end
            else error(40);
            if sym <> rparen Then 
            error(22);
            getsym
        End
    Else 
        if sym = writesym Then
        Begin
            getsym;
            if sym = lparen Then
            Begin
                repeat
                getsym;
                expression([rparen,comma]+fsys);
                gen(wrt,0,0);
                until sym <> comma;
                If sym <> rparen Then
                error(22);
                getsym
            end
            else error(40)
        end;
    ```

5. 在解释执行过程中加入对red和wrt指令的操作,将输出写入output文件
    ```pascal
        red : 
              Begin
                write('> ');
                readln(s[base(l) + a]);
              End;
        wrt : 
              Begin
                writeln(file_output,s[t]);
                t := t + 1;
              End
    ```

6. 在原来的PLO源程序上添加输入输出的语句
    ```pascal
    read(x,y);
    call multiply;
    write(z);
    read(x,y);
    call divide;
    write(q);
    write(q+z);
    call gcd;
    write(z);
    ```

中间代码:

```pascal
    0 const  m = 7, n = 85;
    1 var  x, y, z, q, r;
    1 procedure  multiply;
    1   var  a, b;
    2   begin  a := x;  b := y;  z := 0;
    9     while b > 0 do
   13       begin  
   13         if odd b then z := z + a; 
   20         a := 2*a ;  b := b/2 ;
   28       end
   28   end;
    2  INT    0    5
    3  LOD    1    3
    4  STO    0    3
    5  LOD    1    4
    6  STO    0    4
    7  LIT    0    0
    8  STO    1    5
    9  LOD    0    4
   10  LIT    0    0
   11  OPR    0   12
   12  JPC    0   29
   13  LOD    0    4
   14  OPR    0    6
   15  JPC    0   20
   16  LOD    1    5
   17  LOD    0    3
   18  OPR    0    2
   19  STO    1    5
   20  LIT    0    2
   21  LOD    0    3
   22  OPR    0    4
   23  STO    0    3
   24  LOD    0    4
   25  LIT    0    2
   26  OPR    0    5
   27  STO    0    4
   28  JMP    0    9
   29  OPR    0    0
   30 procedure  divide;
   30   var  w;
   31   begin  r := x;  q := 0;  w := y;
   38     while w <= r do w := 2*w ;
   47     while w > y do
   51     begin  q := 2*q;  w := w/2;
   59       if w <= r then
   62       begin  r := r-w;  q := q+1 end
   71     end
   71   end;
   31  INT    0    4
   32  LOD    1    3
   33  STO    1    7
   34  LIT    0    0
   35  STO    1    6
   36  LOD    1    4
   37  STO    0    3
   38  LOD    0    3
   39  LOD    1    7
   40  OPR    0   13
   41  JPC    0   47
   42  LIT    0    2
   43  LOD    0    3
   44  OPR    0    4
   45  STO    0    3
   46  JMP    0   38
   47  LOD    0    3
   48  LOD    1    4
   49  OPR    0   12
   50  JPC    0   72
   51  LIT    0    2
   52  LOD    1    6
   53  OPR    0    4
   54  STO    1    6
   55  LOD    0    3
   56  LIT    0    2
   57  OPR    0    5
   58  STO    0    3
   59  LOD    0    3
   60  LOD    1    7
   61  OPR    0   13
   62  JPC    0   71
   63  LOD    1    7
   64  LOD    0    3
   65  OPR    0    3
   66  STO    1    7
   67  LOD    1    6
   68  LIT    0    1
   69  OPR    0    2
   70  STO    1    6
   71  JMP    0   47
   72  OPR    0    0
   73 procedure  gcd;
   73   var  f, g ;
   74   begin  f := x;  g := y;
   79     while f <> g do
   83     begin
   83       if f < g then g := g-f;
   91       if g < f then f := f-g;
   99     end;
  100     z := f
  101   end;
   74  INT    0    5
   75  LOD    1    3
   76  STO    0    3
   77  LOD    1    4
   78  STO    0    4
   79  LOD    0    3
   80  LOD    0    4
   81  OPR    0    9
   82  JPC    0  100
   83  LOD    0    3
   84  LOD    0    4
   85  OPR    0   10
   86  JPC    0   91
   87  LOD    0    4
   88  LOD    0    3
   89  OPR    0    3
   90  STO    0    4
   91  LOD    0    4
   92  LOD    0    3
   93  OPR    0   10
   94  JPC    0   99
   95  LOD    0    3
   96  LOD    0    4
   97  OPR    0    3
   98  STO    0    3
   99  JMP    0   79
  100  LOD    0    3
  101  STO    1    5
  102  OPR    0    0
  103 
  103 begin 
  104   x := m;  y := n;  call multiply;
  109   x := 25;  y:= 3;  call divide;
  114   x := 84;  y := 36;  call gcd;
  119   read(x,y);
  121   call multiply;
  122   write(z);
  124   read(x,y);
  126   call divide;
  127   write(q);
  129   write(q+z);
  133   call gcd;
  134   write(z);
  136 
  136 end.
  103  INT    0    8
  104  LIT    0    7
  105  STO    0    3
  106  LIT    0   85
  107  STO    0    4
  108  CAL    0    2
  109  LIT    0   25
  110  STO    0    3
  111  LIT    0    3
  112  STO    0    4
  113  CAL    0   31
  114  LIT    0   84
  115  STO    0    3
  116  LIT    0   36
  117  STO    0    4
  118  CAL    0   74
  119  RED    0    3
  120  RED    0    4
  121  CAL    0    2
  122  LOD    0    5
  123  WRT    0    0
  124  RED    0    3
  125  RED    0    4
  126  CAL    0   31
  127  LOD    0    6
  128  WRT    0    0
  129  LOD    0    6
  130  LOD    0    5
  131  OPR    0    2
  132  WRT    0    0
  133  CAL    0   74
  134  LOD    0    5
  135  WRT    0    0
  136  OPR    0    0
```

输入输出数据

```pascal
$ ./PL0_compile pl0.pas result.txt
> 12
> 20
> 50
> 23

···
START PL/0
240
2
242
1
END PL/0
```
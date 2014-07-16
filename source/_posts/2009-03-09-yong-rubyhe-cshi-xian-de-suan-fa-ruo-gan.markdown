---
layout: post
title: "用Ruby和C实现的算法若干"
date: 2009-03-09 10:13
comments: true
categories: ruby
---


> 算法1：题目：有1、2、3、4个数字，能组成多少个互不相同且无重复数字 的三位数？都是多少？
排列组合的问题
{% codeblock lang:ruby %}
class Array 
  def perm(n) 
    if size < n or n < 0 
    elsif n == 0 
      yield([]) 
    else 
      self[1..-1].perm(n - 1) do |x| 
        (0...n).each do |i| 
          yield(x[0...i] + [first] + x[i..-1]) 
        end 
      end 
      self[1..-1].perm(n) do |x| 
        yield(x) 
      end 
    end 
  end 
end 

quantity=0 
[1,2,3,4].perm(3) do |x| 
  quantity+=1 
  p x 
end 
p quantity

{% endcodeblock %}

---------------------------------------
{% codeblock lang:c %}
#include "stdio.h" 
#define MAX 3 
void main() 
{ 
 int i,j,k; 
 int num=0; 
 int a[3]; 
 int newline=0; 
 i=1; 
 while(i<=MAX) 
 { 
     a[0]=i; 
     j=1; 
     while(j<=MAX) 
         { 
        if(j==i)     {j++; continue;} 
        a[1]=j; 
        k=1; 
        while(k<=MAX) 
         { 
         if(k==i) {k++;continue;} 
         if(k==j) {k++;continue;} 
            a[2]=k; 
            k++; 
            for(int temp=0;temp<3;temp++) 
        printf("%d",a[temp]); 
     printf("    "); 
            num++; 
            newline++; 
            if(newline%10==0) 
                printf("\n"); 
         }//while k 
        j++; 
     }//while j 
             i++; 
        }//while i 
 
 printf("\nTotal number is %d",num); 
 } 
{% endcodeblock %}
<br/>

> 题目2：
验证谷角猜想。日本数学家谷角静夫在研究自然数时发现了一个奇怪现象：对于任意一个自然数 n ，若 n 为偶数，则将其除以 2 ;若 n 为奇数，则将其乘以 3 ，然后再加 1 。如此经过有限次运算后，总可以得到自然数 1 。人们把谷角静夫的这一发现叫做“谷角猜想”。 
编写一个程序，由键盘输入一个自然数 n ，把 n 经过有限次运算后，最终变成自然数 1 的全过程打印出来 
{% codeblock lang:ruby %}
def gu(n) 
    if n==0 
        print "Invalid number!" 
    end 
    while n!=1 
        if n%2==0 
            yield(n) 
            n=n/2 
        elsif n%2!=0 
            yield(n) 
            n=n*3+1 
        end 
    end 
    print n 
end 

puts "Input an Integer(except 0): " 

n=gets.chomp.to_i 
gu(n) {|x| print x, " "} 

{% endcodeblock %}
<br/>
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
  int n; 
        int newline=0; 
loop1: 
  printf("Please input the natural number(>=1):"); 
  scanf("%d",&n); 
  if(n==0)    
  { 
    printf("Invalid number\n"); 
    goto loop1; 
  } 
  while(n!=1) 
  { 
    if(n%2==0)    
    { 
      n=n/2; 
      printf("%d    ",n); 
    } 
    else 
    { 
      n=n*3+1; 
      printf("%d ",n); 
    } 
    newline++; 
    if(newline%10==0) printf("\n"); 
 
  } 
  printf("\nThe total execute number is %d",newline); 
} 
{% endcodeblock %}

<br/>

> 题目3：
从键盘上输入某年某月某日，计算是这一年中的第几天

{% codeblock lang:ruby %}
printf "Input a date: (format:2009.2.14)" 
time_arr=gets.chomp.split(".") 
time=Time.mktime(time_arr[0].to_i,time_arr[1].to_i,time_arr[2].to_i,) 
time.yday
{% endcodeblock %}
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
  int year,month,day; 
  int    i; 
  int total=0; 
  int a[12]={31,0,31,30,31,30,31,31,30,31,30,31}; 
  printf("Please input the date of today:(yyyy,mm,dd)"); 
  scanf("%d,%d,%d",&year,&month,&day); 
  if( year%400 == 0) 
            a[1]=29; 
  else    
     a[1]=28; 
 
        for(i=0;i<month-1;i++) 
    total+=a[i]; 
  total+=day; 
        
  printf("\nToday is number %d day in this year.",total);         
 
} 
{% endcodeblock %}
<br/>

> 题目4：
输入n，打印n阶楼梯，最上层楼梯有两个笑脸
 
{% codeblock lang:ruby %}

def stairs(max) 
    f=max 
    while f!=0     
        yield f 
        f-=1 
    end 
end 

10.times {print "     "} 
print "\1    \1\n" 
stairs(10) do |f|    
    (f-1).times do    
        print "     " 
    end 
    print "_|" 
print "\n" 
end    
{% endcodeblock %}
<br/> 
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
  int i,j; 
  printf("\1 \1\n"); 
  for(i=0;i<11;i++) 
  { 
    for(j=1;j<=i;j++) 
      if(i==j) 
        printf("|_"); 
      else 
        printf(" "); 
         printf("\n"); 
  }             
} 
{% endcodeblock %}
<br/> 

>题目5：
一个整数，它加上100后是一个完全平方数，再加上168又是一个完全平方数，请问该数是多少？

{% codeblock lang:ruby %}
x=17 
while x<1000 
    y=10 
    while y<1000 
        if (x+y)*(x-y)==168 
            puts    x*x-100 
            break 
        else 
            y+=1 
        end 
    end 
    x+=1 
end  
{% endcodeblock %}
<br/>
{% codeblock lang:c %}  

#include "stdio.h" 
#include "math.h" 
void main() 
{ 
  int x; 
  int m,n; 
    
  for(x=0;x<100;x++) 
  { 
    m=sqrt(x+100); 
          n=sqrt(x+268); 
    if( (m*m==x+100) && (n*n==x+268) ) 
    { 
      printf("%d",x); 
      break; 
    } 
  } 
}
{% endcodeblock %}
<br/>

> 题目6：
有一对兔子，从出生后第3个月起每个月都生一对兔子，小兔子长到第三个月后每个月又生一对兔子，假如兔子都不死，问每个月的兔子总数为多少？

{% codeblock lang:ruby %}
##3个月大开始生兔子是fib序列。
class Rabbit 
    @@count=0 
    attr_accessor :age 
    def initialize 
        @age=0 
        @@count+=1 
    end 

    def increment 
        @age+=1 
    end 

    def self.quantity 
        @@count 
    end 
end 

first_rabbit=Rabbit.new 
rabbits=Array.new 
rabbits<<first_rabit 
puts "Input the months:" 
n=gets.chomp.to_i 
n.times do |month| 
    puts "#{month} -- #{Rabbit.quantity*2} rabbits" 
    rabits.each do |rabbit| 
        rabbit.increment 
        if rabbit.age>=3 
            rabbits<<Rabit.new 
        end 
    end 
end 
{% endcodeblock %}
<br/>
{% codeblock lang:ruby %}
def fib(month)
  f1,f2=2,2
  100.times do
    yield(f1)
    f1,f2=f1+f2,f1
  end
end

n=gets.chomp.to_i 
fib(n){|x| puts x}    
{% endcodeblock %}

<br/>

{% codeblock lang:c %}
#include "stdio.h" 
#include "math.h" 
 
void main() 
{ 
  int month; 
  //long int num=1;                            //the number of rabbits 
  int i;                                    //loop variable 
  int f1=1; 
  int f2=1; 
  printf("Which month would you want to konw how many rabbits ?"); 
  scanf("%d",&month); 
        printf("1----1 pair of rabbit\n"); 
  printf("2----1 pairs of rabbits\n"); 
        i=3; 
  while(i<=month) 
  { 
     
    f1=f1+f2; 
    printf("%d----%d pairs of rabbits\n",i,f1); 
    f2=f1+f2; 
    printf("%d----%d pairs of rabbits\n",++i,f2); 
    i++; 
  } 
  //printf("\nThe %d month has %d number rabbits.\n",month,num);         
} 

{% endcodeblock %}
<br/>

> 题目7:
打印出所有的“水仙花数”，所谓“水仙花数”是指一个三位数，其各位数字立方和等于该数本身。例如：153是一个“水仙花数”，因为153=1的三次方＋5的三次方＋3的三次方。
{% codeblock lang:ruby %}
def daffodil 
    for i in (1..9) 
        for j in (0..9) 
            for k in (0..9) 
                x=i*100+j*10+k 
                y=i**3+j**3+k**3 
                if x==y 
                    puts x 
                else 
                    k+=1 
                end 
            end 
        end 
    end 
end 
daffodil

{% endcodeblock %}    
<br/>
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
     int hundreds=1;    
     int tens=1; 
     int units=1; 
     int i,num;    //loop variable 
     int temp_num; 
     for(i=111;i<=999;i++) 
     { 
        num=i; 
        units=num%10; 
        num=num/10; 
        tens=num%10; 
        num=num/10; 
        hundreds=num%10; 
        temp_num=hundreds*hundreds*hundreds+tens*tens*tens+units*units*units;         
        if(temp_num==i) 
         printf("%d    ",i);        

     } 
} 
{% endcodeblock %}
<br/>

> 题目8：将一个正整数分解质因数。例如：输入90,打印出90= 2\*3\*3\*5。
{% codeblock lang:ruby %}
def sub(n) 
    a=Array.new 
    flag=1 
    while flag==1 
        for i in(2..n) 
            if n%i==0 
                a<<i 
                n=n/i 
                break 
            end 
            if i==n-1 and n%i!=0 
                flag=0 
            end 
        end 
    end 
    a 
end 
puts "Input a number:" 
n=gets.chomp.to_i 
puts "#{n}=#{sub(n).join('*')}"    
{% endcodeblock %}
<br/>
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
  int i=2; 
  int num; 
loop1: 
  printf("Please input your number which want to factorization:\n"); 
  scanf("%d",&num); 
  if(num==1) 
  { 
    printf("1 cannot    Integer factorization \n"); 
    goto loop1; 
  } 
  while(i<=num) 
  { 
    if( num%i == 0 ) 
    { 
      printf("%d",i);        
      if(num!=i) 
        printf("*"); 
      if(num==i) 
         break; 
      num=num/i; 
      i=2; 
    } 
    else 
      i++; 
  } 
  printf("\n"); 
} 
{% endcodeblock %}
<br/>

> 题目9：
求两个数的最小公倍数和最大公因数
{% codeblock lang:ruby %}
puts "Input two numbers( split with ","):" 
b=Array.new 
a=gets.chomp.split(',') 
a.each do |elem| 
    b<<elem.to_i 
end 
min_number=b.min 
max_number=b.max 

common_divisor=1 
common_multiple=min_number*max_number 
min_number.downto(1) do |i| 
    if min_number%i==0 and max_number%i==0 
        common_divisor=i 
        break 
    end 
end 

max_number.upto(max_number*min_number) do |i| 
    if i%min_number==0 and i%max_number==0 
        common_multiple=i 
        break 
    end 
end 
puts "The common divisor is #{common_divisor}" 

puts "The common multiple is #{common_multiple}"
{% endcodeblock %}
<br/>
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
  int num1,num2; 
  int temp_num; 
  printf("Please input num1,num2:"); 
  scanf("%d,%d",&num1,&num2); 
  if(num1>num2) 
  { 
    temp_num=num1; 
    num1=num2; 
    num2=temp_num; 
  } 
    
        if(num2%num1==0) 
    printf("The greatest common divisor is %d\n",num1); 
  else 
  { 
            for(int i=num1;i>=1;i--) 
        if( (num1%i==0) && (num2%i==0) ) 
        { 
         printf("The greatest common divisor is %d    \n",i); 
         break; 
        } 
  } 
         
        
  if(num2%num1==0) 
    printf("The    least common multiple is %d\n",num2); 
  else 
    for(int j=num2;j<num1*num2;j++) 
      if( j%num1==0 && j%num2==0) 
      { 
        printf("The least common multiple is %d\n",j); 
        break; 
      } 
 
} 
{% endcodeblock %}
<br/>

> 题目10：求s=a+aa+aaa+aaaa+aa...a的值，其中a是一个数字。例如2+22+222+2222+22222(此时共有5个数相加)，几个数相加有键盘控制。

{% codeblock lang:ruby %}
    a=Array.new 
    gets.chomp.split(",").each do |i| 
      a<<i.to_i 
    end 
    elem=[0] 
    for n in (1..a[1]) do 
        elem[n]=elem[n-1]+a[0]*10**(n-1) 
        print elem[n],"+" 
    end 
    print "=#{elem.inject(0) { |sum,i| sum+=i}}"    
{% endcodeblock %}

<br/> 

{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
         
  int a; 
  int count; 
  int *temp; //有多少个数相加，就分配多大的内存 
  int i=1; 
        int j=0; 
  printf("Please input number a:"); 
  scanf("%d",&a); 
  printf("Please input the times:"); 
  scanf("%d",&count); 
 
  temp=new int[count]; 
  temp[0]=a; 
     if(count==1) 
  { 
     printf("%d",a); 
     goto loop1; 
  } 
  for( i=1;i<count;i++) 
  { 
    temp[i]=temp[i-1]*10+a; 
  } 
  a=0; 
         for(j=0;j<count;j++) 
    { 
     printf("%d",temp[j]); 
            if(j!=(count-1)) 
         printf("+"); 
        
                 a+=temp[j]; 
    } 
 
loop1: printf("=%d",a); 
} 
{% endcodeblock %}
<br/>

> 题目11：
一个数如果恰好等于它的因子之和，这个数就称为“完数”。例如6=1＋2＋3.编程找出1000以内的所有完数。

{% codeblock lang:ruby %}
    a=Array.new 
    for i in (1..1000) 
      a[i]=[] 
      for j in (1..i/2) 
        if i%j==0 
          a[i] << j 
        end
      end
      if i==a[i].inject(0){|sum,elem| sum+=elem} 
        puts i 
      end 
    end
{% endcodeblock %}
<br/> 
{% codeblock lang:c %}
#include "stdio.h" 
#include "math.h" 
#define MAX 1000 
void main() 
{ 
  int temp[MAX/2]; 
  int sum=0; 
  int k; 
  for(int m=0;m<MAX/2;m++) //初始化 
    temp[m]=0; 
  for(int i=2;i<MAX;i++) 
  { 
    k=0; 
    for(int j=1;j<=floor(i/2);j++) 
    { 
      if(i%j==0) 
      { 
        temp[k]=j; 
        sum+=j; 
        k++; 
      }//if 
        
    } 
 
     
    if(sum==i) 
    { 
      printf("\n%d=",i); 
      for(k=0;k<MAX/2;k++) 
      {                
        printf("%d",temp[k]); 
        if(temp[k+1]==0) 
          break; 
        printf("+"); 
      }//for 
      sum=0; 
      for(int m=0;m<MAX/2;m++)    
                                temp[m]=0;    
    }//if 
    else    
    { 
      sum=0;                         
    } 
  }//for 
}//main 
{% endcodeblock %}
 
<br/>

> 题目12：
输入高度，输出杨辉三角
{% codeblock lang:ruby %}
def yang_triangle(n) 
    a=[] 
    for f in (0..n-1) 
        a[f]=[] 
        for n in (0..f+1) 
            a[f][0]=1 
            if f>1 
                for m in (1..n-2) 
                    a[f][m]=a[f-1][m-1]+a[f-1][m] 
                end 
            end 
            a[f][n-1]=1 
        end 
    end 
    a 
end 
puts "Input a number:" 
n=gets.chomp.to_i 
yang_triangle(n).each do |arr| 
    arr.each do |elem| 
        print elem, " " 
    end 
end
{% endcodeblock %}
<br/>
{% codeblock lang:c %}
#include "stdio.h" 
void main() 
{ 
  int n; //杨辉三角的阶数 
  int **c;    //存储杨辉三角的二维数组 
  int i,j; //二层循环变量 
  int k; 
  printf("Please input the layer of yanghui:"); 
  scanf("%d",&n); 
  c=new int* [n];    //动态分配二维空间 
        for(k=0;k<n;k++) 
    c[k]=new int[n]; 
 
  for(k=0;k<n;k++)            
     { 
    c[k][0]=1; 
         c[k][k]=1; 
     } 
  for(i=2;i<n;i++) 
     for(j=1;j<i;j++) 
    c[i][j]=c[i-1][j-1]+c[i-1][j]; 
    
 
        for(i=0;i<n;i++)                 //输出结果 
  { 
    for(j=0;j<=i;j++) 
    { 
      printf("%d ",c[i][j]); 
    } 
    printf("\n"); 
  } 
} 
{% endcodeblock %}
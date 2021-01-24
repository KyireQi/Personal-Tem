# 线性代数
---
## 高斯消元法
高斯消元法主要用于求解**线性方程组**，也可以求**矩阵的秩、矩阵的逆**等
下面有两种高斯消元的写法，整数方程组和异或方程组
**整数代码：**
```cpp
int a[N][N];//增广矩阵
int x[N];//解集
bool freeX[N];//标记是否为自由变元
int GCD(int a,int b){
    return !b?a:GCD(b,a%b);
}
int LCM(int a,int b){
    return a/GCD(a,b)*b;
}
int Gauss(int equ,int var){//返回自由变元个数
    /*初始化*/
    for(int i=0;i<=var;i++){
        x[i]=0;
        freeX[i]=true;
    }
 
    /*转换为阶梯阵*/
    int col=0;//当前处理的列
    int row;//当前处理的行
    for(row=0;row<equ&&col<var;row++,col++){//枚举当前处理的行
        int maxRow=row;//当前列绝对值最大的行
        for(int i=row+1;i<equ;i++){//寻找当前列绝对值最大的行
            if(abs(a[i][col])>abs(a[maxRow][col]))
                maxRow=i;
        }
        if(maxRow!=row){//与第row行交换
            for(int j=row;j<var+1;j++)
                swap(a[row][j],a[maxRow][j]);
        }
        if(a[row][col]==0){//col列第row行以下全是0，处理当前行的下一列
            row--;
            continue;
        }
 
        for(int i=row+1;i<equ;i++){//枚举要删去的行
            if(a[i][col]!=0){
                int lcm=LCM(abs(a[i][col]),abs(a[row][col]));
                int ta=lcm/abs(a[i][col]);
                int tb=lcm/abs(a[row][col]);
                if(a[i][col]*a[row][col]<0)//异号情况相加
                    tb=-tb;
                for(int j=col;j<var+1;j++) {
                    a[i][j]=a[i][j]*ta-a[row][j]*tb;
                }
            }
        }
    }
 
    /*求解*/
    //无解：化简的增广阵中存在(0,0,...,a)这样的行，且a!=0
    for(int i=row;i<equ;i++)
        if (a[i][col]!=0)
            return -1;
 
    //无穷解: 在var*(var+1)的增广阵中出现(0,0,...,0)这样的行
    int temp=var-row;//自由变元有var-row个
    if(row<var)//返回自由变元数
        return temp;
 
    //唯一解: 在var*(var+1)的增广阵中形成严格的上三角阵
    for(int i=var-1;i>=0;i--){//计算解集
        int temp=a[i][var];
        for(int j=i+1;j<var;j++){
            if(a[i][j]!=0)
                temp-=a[i][j]*x[j];
        }
        if(temp%a[i][i]!=0)//有浮点数解，无整数解
            return -2;
        x[i]=temp/a[i][i];
    }
    return 0;
}
 
int main(){
    int equ,var;//equ个方程，var个变元
    while(scanf("%d%d",&equ,&var)!=EOF) {
 
        memset(a,0,sizeof(a));
        for(int i=0;i<equ;i++)//输入增广矩阵
            for(int j=0;j<var+1;j++)
                scanf("%d",&a[i][j]);
 
 
        int freeNum=Gauss(equ,var);//自由元个数
        if(freeNum==-1)//无解
            printf("无解\n");
        else if(freeNum==-2)//有浮点数解无整数解
            printf("无整数解\n");
        else if(freeNum>0){//有无穷多解
            printf("有无穷多解，自由变元个数为%d\n",freeNum);
            for(int i=0;i<var;i++){
                if(freeX[i])
                    printf("x%d是自由变元\n",i+1);
                else
                    printf("x%d=%d\n",i+1,x[i]);
            }
        }
        else{//有唯一解
            for(int i=0;i<var;i++)
                printf("x%d=%d\n",i+1,x[i]);
        }
        printf("\n");
    }
    return 0;
}
```
**异或方程组求解：**
```cpp
int a[N][N];//增广矩阵
int x[N];//解集
int freeX[N];//自由变元
int Gauss(int equ,int var){//返回自由变元个数
    /*初始化*/
    for(int i=0;i<=var;i++){
        x[i]=0;
        freeX[i]=0;
    }
 
    /*转换为阶梯阵*/
    int col=0;//当前处理的列
    int num=0;//自由变元的序号
    int row;//当前处理的行
    for(row=0;row<equ&&col<var;row++,col++){//枚举当前处理的行
        int maxRow=row;//当前列绝对值最大的行
        for(int i=row+1;i<equ;i++){//寻找当前列绝对值最大的行
            if(abs(a[i][col])>abs(a[maxRow][col]))
                maxRow=i;
        }
        if(maxRow!=row){//与第row行交换
            for(int j=row;j<var+1;j++)
                swap(a[row][j],a[maxRow][j]);
        }
        if(a[row][col]==0){//col列第row行以下全是0，处理当前行的下一列
            freeX[num++]=col;//记录自由变元
            row--;
            continue;
        }
 
        for(int i=row+1;i<equ;i++){
            if(a[i][col]!=0){
                for(int j=col;j<var+1;j++){//对于下面出现该列中有1的行，需要把1消掉
                    a[i][j]^=a[row][j];
                }
            }
        }
    }
 
    /*求解*/
    //无解：化简的增广阵中存在(0,0,...,a)这样的行，且a!=0
    for(int i=row;i<equ;i++)
        if(a[i][col]!=0)
            return -1;
 
    //无穷解: 在var*(var+1)的增广阵中出现(0,0,...,0)这样的行
    int temp=var-row;//自由变元有var-row个
    if(row<var)//返回自由变元数
        return temp;
 
    //唯一解: 在var*(var+1)的增广阵中形成严格的上三角阵
    for(int i=var-1;i>=0;i--){//计算解集
        x[i]=a[i][var];
        for(int j=i+1;j<var;j++)
            x[i]^=(a[i][j]&&x[j]);
    }
    return 0;
}
int enumFreeX(int freeNum,int var){//枚举自由元，统计有解情况下1最少的个数
    int sta=(1<<(freeNum));//自由元的状态总数
    int res=INF;
    for(int i=0;i<sta;++i){//枚举状态
        int cnt=0;
        for(int j=0;j<freeNum;j++){//枚举自由元
            if(i&(1<<j)){
                cnt++;
                x[freeX[j]]=1;
            }else
                x[freeX[j]]=0;
        }
        for(int k=var-freeNum-1;k>=0;k--){//没有自由元的最下面一行
            int index=0;
            for(index=k;k<var;index++){//在当前行找到第一个非0自由元
                if(a[k][index])
                    break;
            }
            x[index]=a[k][var];
            for(int j=index+1;j<var;++j){//向后依次计算出结果
                if(a[k][j])
                    x[index]^=x[j];
            }
            cnt+=x[index];//若结果为1，则进行统计
        }
        res=min(res,cnt);
    }
    return res;
}
int main(){
    memset(a,0,sizeof(a));
 
    int equ,var;
    scanf("%d%d",&equ,&var);
    for(int i=0;i<equ;i++){//构造初始状态
 
    }
    for(int i=0;i<equ;i++)//最终状态
        scanf("%d",&a[i][var]);
 
    int freeNum=Gauss(equ,var);//获取自由元
    if(freeNum==-1)//无解
        printf("inf\n");
    else if(freeNum==0){//唯一解
        int res=0;
        for(int i=0;i<var;i++)
            res+=x[i];
        printf("%d\n",res);
    }
    else{//多个解
        int res=enumFreeX(freeNum,var);
        printf("%d\n",res);
    }
    return 0;
}
```
一般在求解解的个数的问题时，只需要把自由元的数目$cnt$求出来然后$2 ^ {cnt}$就是答案。

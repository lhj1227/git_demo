问题一（不取模情况）

#### 在B（k,n）中k,n取多大可行？首先令n=4,k=3,n表示有四个变量：x1 x2 x3 x4 ,k表示f函数的次数最高为三次，因此有：

​        维数：1+C(1,4)+C(2,4)+C(3,4)=1+4+6+4=15 ，即有：x1 x2 x3 ....x15

​        可逆矩阵A：P(2,n)=P(2,4)=12 

​        向量b: n个取值=4

​        operations=12+4=16

代码块：

```python
import numpy as np
from numpy import array
import itertools
import sympy
from sympy import symbols

# operation是列加法或取非. （1，2）表示第2列加到第1列，（1，0）表示对第一列取非（第二个元素是0表示取非，不是0表示列加法）
# present_status是当前向量状态(x1,x2,x3,...,x15)， next_status是经过operation更新之后的状态向量。


x1, x2, x3, x4, x5, x6, x7, x8, x9, x10 = symbols('x1 x2 x3 x4 x5 x6 x7 x8 x9 x10')
x11, x12, x13, x14, x15 = symbols('x11 x12 x13 x14 x15 ')


monomial3 = []  # 所有三次多项式，共有4个
for i in itertools.combinations('1234', 3):
    i = array(i)
    tem = []
    for j in range(3):
        tem.append(int(i[j]))
    monomial3.append(tem)
# print('monomial3', monomial3)

monomial2 = []  # 所有二次多项式，共有6个
for i in itertools.combinations('1234', 2):
    i = array(i)
    tem = []
    for j in range(2):
        tem.append(int(i[j]))
    monomial2.append(tem)
# print('monomial2', monomial2)

monomial1 = [] #所有的一次多项式，共有4个
for i in itertools.combinations('1234', 1):
    i = array(i)
    tem = []
    for j in range(1):
        tem.append(int(i[j]))
    monomial1.append(tem)
# print('monomial1', monomial1)

monomual0 = [[]]#所有的零次多项式

def update(operation):
    present_status = [x1, x2, x3, x4, x5, x6, x7, x8, x9, x10, x11, x12, x13, x14, x15]
    m3 = np.zeros((4, 4, 4), dtype=list)  # m3用来储存三次多项式的系数
    m2 = np.zeros((4, 4), dtype=list)  # m2用来储存二次多项式的系数
    m1 = mp.zeros((4),dtype=list) #m1用来存储一次多项式的系数
    for i in range(4):
        tem = monomial3[i].copy()
        m3[tem[0] - 1][tem[1] - 1][tem[2] - 1] = present_status[i]
    for j in range(6):
        tem = monomial2[j].copy()
        m2[tem[0] - 1][tem[1] - 1] = present_status[j + 4]
    for k in range(4):
        tem = monomial1[k].copy()
        m1[tem[0] - 1] = present_status[k+10]
    # print('m3', m3)
    # print('m2', m2)
    qiuhou = []  # 所有需要改变的系数以及位置
    # m = len(present_status)
    if operation[1] == 0: # 0表示取非
        for i in range(35):
            if operation[0] in set(monomial3[i]):
                tem = monomial3[i].copy()
                # print('tem', tem)
                tem.remove(operation[0])
                qiuhou.append([tem, monomial3[i]])
    else:
        for i in range(35):
            if operation[0] in set(monomial3[i]):
                tem = monomial3[i].copy()
                pos = tem.index(operation[0])
                tem[pos] = operation[1]
                qiuhou.append([tem, monomial3[i]])
        for j in range(21):
            if operation[0] in set(monomial2[j]):
                tem = monomial2[j].copy()
                pos = tem.index(operation[0])
                tem[pos] = operation[1]
                qiuhou.append([tem, monomial2[j]])
    # print('qiuhou', qiuhou)
    for i in range(len(qiuhou)):
        tem = qiuhou[i].copy()
        if len(tem[0]) == 3 and len(tem[0]) == len(set(tem[0])):
            tem[0] = sorted(tem[0])
            m3[tem[0][0] - 1][tem[0][1] - 1][tem[0][2] - 1] = (m3[tem[0][0] - 1][tem[0][1] - 1][tem[0][2] - 1] +
                                                               m3[tem[1][0] - 1][tem[1][1] - 1][tem[1][2] - 1])
            continue
        if len(tem[0]) == 3 and len(tem[0]) != len(set(tem[0])):
            tem2 = sorted(list(set(tem[0])))
            # print('tem2', tem2)
            m2[tem2[0] - 1][tem2[1] - 1] = (m2[tem2[0] - 1][tem2[1] - 1] + m3[tem[1][0] - 1][tem[1][1] - 1][
                tem[1][2] - 1])
            continue
        if len(set(tem[0])) == 2 and len(tem[1]) == 3:
            tem[0] = sorted(tem[0])
            m2[tem[0][0] - 1][tem[0][1] - 1] = (m2[tem[0][0] - 1][tem[0][1] - 1] + m3[tem[1][0] - 1][tem[1][1] - 1][
                tem[1][2] - 1])
            continue
        if len(set(tem[0])) == 2 and len(tem[1]) == 2:
            tem[0] = sorted(tem[0])
            m2[tem[0][0] - 1][tem[0][1] - 1] = (m2[tem[0][0] - 1][tem[0][1] - 1] + m2[tem[1][0] - 1][tem[1][1] - 1])
            continue

    next_status = []
    for i in range(35):
        tem = monomial3[i].copy()
        next_status.append(m3[tem[0] - 1][tem[1] - 1][tem[2] - 1])
    for j in range(21):
        tem = monomial2[j].copy()
        next_status.append(m2[tem[0] - 1][tem[1] - 1])
    for i in range(len(next_status)):
        next_status[i] = str(next_status[i])
    return next_status


def trans2nusmv(next_status):
    # print(next_status)
    nusmv_format = []
    num_str = {'0', '1', '2', '3', '4', '5', '6', '7', '8', '9'}
    for i in range(len(next_status)):
        tem = next_status[i]
        single_term = ''
        j = 0
        while j < len(tem):
            if j + 1 < len(tem):
                if (tem[j] in num_str) and not (tem[j + 1] in num_str):
                    single_term = single_term + ' xor x[{}]'.format(tem[j])
                    j = j + 1
                    continue
                if (tem[j] in num_str) and (tem[j + 1] in num_str):
                    single_term = single_term + ' xor x[{}{}]'.format(tem[j], tem[j + 1])
                    j = j + 2
                    continue
            if j + 1 >= len(tem):
                if tem[j] in num_str:
                    single_term = single_term + ' xor x[{}]'.format(tem[j])
                    j = j + 1
                    continue
            j = j + 1
        for i in range(5):
            single_term = single_term[1:]
        # print("single = {}".format(single_term))
        nusmv_format.append(single_term)
    return nusmv_format


def target_vec2tf(v):
    x = ''
    for i in range(len(v)):
        if v[i] == 0:
            x = x + 'x[{}] = {} & '.format(i + 1, 'FALSE')
        if v[i] == 1:
            x = x + 'x[{}] = {} & '.format(i + 1, 'TRUE')
    x = x[:-1]
    x = x[:-1]
    return x


def init_vec2tf(v):
    x = ''
    for i in range(len(v)):
        if v[i] == 0:
            x = x + 'init(x[{}]) := {}; '.format(i + 1, 'FALSE')
        if v[i] == 1:
            x = x + 'init(x[{}]) := {}; '.format(i + 1, 'TRUE')
    x = x[:-1]
    x = x[:-1]
    return x

#根据n的大小可以写出所有的操作，即操作的个数是可以根据n的规模写出的
op = list(itertools.permutations([1, 2, 3, 4, 5, 6, 7], 2))
print("op[0]", op[0])
for i in range(7):
    op.append([i+1, 0])
print(op)
#该处的56维是根据问题的规模得到的，还涉及到是否取模的问题
for i in range(56):
    print("next(x[{}]) := \n    case".format(i+1))
    for k in range(len(op)): #k表示操作的个数
        next_status = update(op[k])
        next_status_nusmv = trans2nusmv(next_status)
        print("     (operations = op{})   :   {};".format(k, next_status_nusmv[i]))
    print("    esac;")

```


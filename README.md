# test1
Apriori algorithm with association rule
# coding: utf-8
# Read csv in the form of dataframe
'''import pandas as pd
data=pd.read_csv('Apriori.csv')
#data.list <- split(data.df, seq(nrow(data.df)))
data=data.values.tolist()
data'''
# In[100]:


import csv
import sys
with open('Apriori.csv','r') as f:
  reader=csv.reader(f)
  data=list(reader)
data


# In[101]:


# to remove nan
'''for i in range(len(data)):
    data[i]=[x for x in data[i] if str(x) != 'nan']
data'''


# In[102]:


def createC1(dataSet):
    C1 = []
    for transaction in dataSet:
        for item in transaction:
            if not [item] in C1:
                C1.append([item])
                
    C1.sort()
    return list(map(frozenset, C1))#use frozen set so we
                            #can use it as a key in a dict  


# In[103]:


#D is a dataset in the setform.
D = list(map(set,data))
D


# In[104]:


def scanD(D, Ck, minSupport):
    ssCnt = {}
    for tid in D:
        for can in Ck:
            if can.issubset(tid):
                if not can in ssCnt: ssCnt[can]=1
                else: ssCnt[can] += 1
    numItems = float(len(D))
    retList = []
    supportData = {}
    ssCnt
    for key in ssCnt:
        support = ssCnt[key]#/numItems
        if support >= minSupport:
            retList.insert(0,key)
        supportData[key] = support
    return retList, supportData


# In[105]:


C1 = createC1(data)
C1


# In[106]:


L1,suppDat0 = scanD(D,C1,2)
L1


# In[107]:


def aprioriGen(Lk, k): #creates Ck
    retList = []
    lenLk = len(Lk)
    for i in range(lenLk):
        for j in range(i+1, lenLk): 
            L1 = list(Lk[i])[:k-2]; L2 = list(Lk[j])[:k-2]
            L1.sort(); L2.sort()
            if L1==L2: #if first k-2 elements are equal
                retList.append(Lk[i] | Lk[j]) #set union
    return retList


# In[108]:


def apriori(dataSet, minSupport = 2):
    C1 = createC1(dataSet)
    D = list(map(set, dataSet))
    L1, supportData = scanD(D, C1, minSupport)
    L = [L1]
    k = 2
    while (len(L[k-2]) > 0):
        Ck = aprioriGen(L[k-2], k)
        Lk, supK = scanD(D, Ck, minSupport)#scan DB to get Lk
        supportData.update(supK)
        L.append(Lk)
        k += 1
    return L, supportData


# In[109]:


L,suppData = apriori(data)
L


# In[110]:


def generateRules(L, supportData, minConf=0.6):  #supportData is a dict coming from scanD
    bigRuleList = []
    for i in range(1, len(L)):#only get the sets with two or more items
        for freqSet in L[i]:
            H1 = [frozenset([item]) for item in freqSet]
            if (i > 1):
                rulesFromConseq(freqSet, H1, supportData, bigRuleList, minConf)
            else:
                calcConf(freqSet, H1, supportData, bigRuleList, minConf)
    return bigRuleList   


# In[111]:


def calcConf(freqSet, H, supportData, brl, minConf=0.6):
    prunedH = [] #create new list to return
    for conseq in H:
        conf = supportData[freqSet]/supportData[freqSet-conseq] #calc confidence
        if conf >= minConf: 
            print (freqSet-conseq,'-->',conseq,'conf:',conf)
            brl.append((freqSet-conseq, conseq, conf))
            prunedH.append(conseq)
    return prunedH


# In[112]:


def rulesFromConseq(freqSet, H, supportData, brl, minConf=0.6):
    m = len(H[0])
    if (len(freqSet) > (m + 1)): #try further merging
        Hmp1 = aprioriGen(H, m+1)#create Hm+1 new candidates
        Hmp1 = calcConf(freqSet, Hmp1, supportData, brl, minConf)
        if (len(Hmp1) > 1):    #need at least two sets to merge
            rulesFromConseq(freqSet, Hmp1, supportData, brl, minConf)


# In[113]:


L,suppData= apriori(data,minSupport=2)
L


# In[114]:


rules= generateRules(L,suppData, minConf=0.6)



 Apriori-/README.md 

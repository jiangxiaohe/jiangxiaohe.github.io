---
layout: post
title: LeetCode《字节跳动》组题
tags:
- DSA
categories: algorithm
description: 包含面试各种基本题目
---

机遇从不垂青无准备之人，熟悉java编程，并拾起刷题能力

# 挑战字符串
## 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

题解：总的来说，要用双指针法来表明子串元素的开始和结束位置，区别在于用set或map或Boolean数组来表明子串存在元素。


```java
//用map方法
class Solution {
    public static int lengthOfLongestSubstring(String s) {
        Map<Character,Integer> map=new HashMap<>();
        int i=0,j=0;
        int max=0;
        while(j<s.length()) {
            char c = s.charAt(j);
            if (!map.containsKey(c)) {
                map.put(c, j);
                j++;
            } else {
                int t = map.get(c);
                map.replace(c,j);
                while (i < t) {
                    map.remove(s.charAt(i));
                    i++;
                }
                i++;
                j++;
            }
            max = max > j - i ? max : j - i;
        }
        return max;
    }
}
```

```java
//用set方法
class Solution {  
    public int lengthOfLongestSubstring(String s) {
        int len = s.length();
        int childLen = 0, i = 0, j = 0;
        Set<Character> set = new HashSet<Character>();
        while (i < len && j < len) {
            if (!set.contains(s.charAt(j))) {
                set.add(s.charAt(j++));
                childLen  = Math.max(childLen, j - i);
            } else {
                set.remove(s.charAt(i++));
            }
        }
        return childLen;
    }
}
```

```java
//数组方法
class Solution {
    public static int lengthOfLongestSubstring(String s) {
        int[] exist=new int[256];
        for(int i=0;i<256;i++)exist[i]=-1;
        int i=0,j=0;
        int max=0;
        while(j<s.length()) {
            int c = s.charAt(j);
            if (exist[s.charAt(j)]==-1) {
                exist[c]=j++;
                max=Math.max(max,j-i);
            } else {
                exist[s.charAt(i++)]=-1;
            }
        }
        return max;
    }
}
```

## 最长公共前缀

编写一个函数来查找字符串数组中的最长公共前缀。

题解：难度不大，注意边界条件即可

```java
public static String longestCommonPrefix(String[] strs) {
    String ans="";
    if(strs.length==0)return ans;
    int i=0;
    boolean end=false;
    while(true) {
        if(i>=strs[0].length())break;
        char c = strs[0].charAt(i);
        for (int j = 1; j < strs.length; j++) {
            if (!(i < strs[j].length()&&strs[j].charAt(i)==c) ){
                return strs[0].substring(0,i);
            }
        }
        i++;
    }   
    return str[0];
}
```

## 字符串的排列

给定两个字符串 s1 和 s2，写一个函数来判断 s2 是否包含 s1 的排列。

换句话说，第一个字符串的排列之一是第二个字符串的子串。

>
输入: s1 = "ab" s2 = "eidbaooo"
输出: True
解释: s2 包含 s1 的排列之一 ("ba").

题解：刚开始想到的是建立一个set存储s1的全排列，然后一次判断set中的元素是否在s2中，复杂度为`n1!+n1!*log(n1!)+n1!*log(n1!)*(n1+n2)`。显然，这么高的复杂度超时了。
换一种思路，截取s2中和s1长度相同的片段，然后判断各个字母的个数是否相同，相同即可判定为s1是s2的排列。

注意：复杂度超时的方法中要注意到不管是char[]还是set，参数传递时都是传递的引用，如果要传递值，请用clone()方法。

```java
//超时算法
class Solution {
    public static void swap(int i,int j,char[] s){
        char t=s[i];
        s[i]=s[j];
        s[j]=t;
    }
    public static void DFS(int r,char[] s,Set<char[]> set){
        if(r==s.length){
//            System.out.println(s);
            set.add(s.clone());
//            System.out.println(set.size());
        }
        for(int i=r;i<s.length;i++) {
            swap(r, i, s);
            DFS(r + 1, s, set);
            swap(r, i, s);
        }
    }
    public static boolean checkInclusion(String s1, String s2) {
        Set<char[]> set=new HashSet<>();
        char[] s=s1.toCharArray();
        DFS(0,s,set);

//        System.out.println(set.size());
        for(char[] t:set){
//            System.out.println(new String(t));
//            System.out.println(s2.contains(new String(t)));

            if(s2.contains(new String(t))){
                return true;
            }
        }
        return false;
    }
}
```

```java
class Solution {
    public static boolean checkNum(int[] n1,int[] n2){
        for(int i=0;i<n1.length;i++)
            if(n1[i]!=n2[i])
                return false;
        return true;
    }
    public static boolean checkInclusion(String s1, String s2) {
        if(s1.length()>s2.length())return false;
        int i=0,j=s1.length();
        int[] n1=new int[26],n2=new int[26];
        for(int k=0;k<s1.length();k++)n1[s1.charAt(k)-'a']++;
        for(int k=0;k<s1.length();k++)n2[s2.charAt(k)-'a']++;
        while(true){
            if(checkNum(n1,n2))
                return true;            
            if(j<s2.length())n2[s2.charAt(j++)-'a']++;
            else break;
            n2[s2.charAt(i++)-'a']--;
        }
        return false;
    }
}
```

## 字符串相乘

给定两个以字符串形式表示的非负整数 num1 和 num2，返回 num1 和 num2 的乘积，它们的乘积也表示为字符串形式。

说明：
1. num1 和 num2 的长度小于110。
1. num1 和 num2 只包含数字 0-9。
1. num1 和 num2 均不以零开头，除非是数字 0 本身。
1. 不能使用任何标准库的大数类型（比如 BigInteger）或直接将输入转换为整数来处理。

题解：数组大数加法和乘法的运用，还是有点生疏了，写的太慢了

```java
class Solution {
    public static void add(int[] t,int[] ans,int r){
        int flag=0;
        int i=0;
        for(;i<t.length;i++){
            flag+=ans[i+r]+t[i];
            ans[i+r]=flag%10;
            flag/=10;
        }
        while(flag!=0){
            flag+=ans[i+r];
            ans[i+r]=flag%10;
            flag/=10;
            i++;
        }
    }
    public static void multiply(int[] n1,int[] n2,int r,int[] ans){
        int[] t=new int[n1.length+1];
        int flag=0;
        int i=0;
        for(;i<n1.length;i++){
            flag+=n1[i]*n2[r];
            t[i]=flag%10;
            flag/=10;
        }
        while(flag!=0){
            t[i]=flag%10;
            flag/=10;
            i++;
        }
        add(t,ans,r);
    }
    public static String multiply(String num1, String num2) {
        if(num1.equals("0"))return num1;
        if(num2.equals("0"))return num2;
        int[] n1=new int[num1.length()],n2=new int[num2.length()],ans=new int[n1.length+n2.length];
        for(int i=0;i<num1.length();i++)n1[num1.length()-1-i]=num1.charAt(i)-'0';
        for(int i=0;i<num2.length();i++)n2[num2.length()-1-i]=num2.charAt(i)-'0';
        int len1=num1.length(),len2=num2.length();
        for(int i=0;i<n2.length;i++){
            multiply(n1,n2,i,ans);
        }
        StringBuilder t=new StringBuilder();
        int i=ans.length-1;
        while(ans[i]==0)i--;
        for(;i>=0;i--)t.append(ans[i]);
        return t.toString();
    }
}
```

## 翻转字符串里的单词

给定一个字符串，逐个翻转字符串中的每个单词。

题解：反转两次即可，第一次全体反转，第二次对于每个单词进行反转，并同时舍去多余的空格。第二种是采用java类库中的split方法，

```java
//两次翻转
public class Solution {
    public static void swap(char[] chr,int i,int j){
        char t=chr[i];
        chr[i]=chr[j];
        chr[j]=t;
    }
    public static void swapAll(char[] chr,int i,int j){
        if(i>=j)return;
        for(int k=i;k<=(i+j)/2;k++)
            swap(chr,k,i+j-k);
    }
    public static String reverseWords(String s) {
        if(s.length()==0)return "";
        char[] chr=s.toCharArray();
        swapAll(chr,0,chr.length-1);
        int i=0,j=0;
        while(i<chr.length){
            while(i<chr.length&&chr[i]==' ')i++;
            j=i;
            while(j<chr.length&&chr[j]!=' ')j++;
            swapAll(chr,i,j-1);
            i=j;
        }
        StringBuilder sb=new StringBuilder();
        i=0;
        boolean flag=false;
        while(i<chr.length){
            if(chr[i]==' '){
                if(flag){
                    sb.append(' ');
                    flag=false;
                }
            }else{
                sb.append(chr[i]);
                flag=true;
            }
            i++;
        }
        if(sb.length()==0)return "";
        if(sb.charAt(sb.length()-1)==' ')
            return sb.toString().substring(0,sb.length()-1);
        else
            return sb.toString();
    }
}
```

```java
//采用库中的split和trim方法
public class Solution {
    public String reverseWords(String s) {
        String[] s1=s.split(" ");
        StringBuilder ans=new StringBuilder();
        for(int i=s1.length-1;i>=0;i--){
        	if (s1[i]!=" " && !s1[i].equals("") ) {
					ans.append(s1[i]);
	                ans.append(" ");
			}
        }
        return ans.toString().trim();
    }
}
```

## 简化路径
以 Unix 风格给出一个文件的绝对路径，你需要简化它。或者换句话说，将其转换为规范路径。

请注意，返回的规范路径必须始终以斜杠 / 开头，并且两个目录名之间必须只有一个斜杠 /。最后一个目录名（如果存在）不能以 / 结尾。此外，规范路径必须是表示绝对路径的最短字符串。

用数组或者List来将目录存储在栈中。测试可得，数组的速度明显高于List的速度，ArrayList和LinkedList的速度差不多。

```java
class Solution {
    public static String simplifyPath(String path) {
        String[] strs=path.split("/");
        String[] list=new String[100];
        int size=0;
//        for(String s:strs) System.out.println(s);
        StringBuilder sb=new StringBuilder();
        for(int i=0;i< strs.length;i++){
            String s=strs[i];
            if(!s.isEmpty()){
                if(s.equals(".")){
                    continue;
                }else if(s.equals("..")){
                    size--;
                    if(size<0)size=0;
                }else{
                    list[size++]=s;
                }
            }
        }
        if(size==0)return "/";
        for(int i=0;i<size;i++)sb.append("/"+list[i]);
        return sb.toString();
    }
}
```

## 复原IP地址

给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。

* 输入: "25525511135"
* 输出: ["255.255.11.135", "255.255.111.35"]

题解：暴力递归法，注意剪枝

```java
public static void fun(String s,List<String> ans,int step,int[] index){
    if(step==4&&Integer.parseInt(s.substring(index[3],s.length()))<=255){
        if(!(s.length()-index[3]>1&&s.charAt(index[3])=='0')) {
            StringBuilder sb = new StringBuilder();
            sb.append(s.substring(index[0], index[1]) + ".");
            sb.append(s.substring(index[1], index[2]) + ".");
            sb.append(s.substring(index[2], index[3]) + ".");
            sb.append(s.substring(index[3], s.length()));
            ans.add(sb.toString());
//                System.out.println(sb.toString());
        }
        return;
    }
    if(step==4)return;
    for(int i=1;i<4;i++) {
        index[step] = index[step - 1] + i;
        if (index[step]<s.length()&&Integer.parseInt(s.substring(index[step - 1], index[step])) <= 255 && s.length() - index[step] >= 4 - step && s.length() - index[step] <= 3 * (4 - step)) {
            if(i!=1&&s.charAt(index[step-1])=='0')continue;
            fun(s, ans, step + 1, index);
        }
    }
}
public static List<String> restoreIpAddresses(String s) {
    List<String> ans=new LinkedList<>();
    int step=1;
    int[] index=new int[4];
    if(s.length()>=4&&s.length()<=12)
        fun(s,ans,step,index);
    return ans;
}
```

# 数组与排序

## 三数之和

给定一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？找出所有满足条件且不重复的三元组。

思路：先排序，然后固定一个数，双指针找另外两个数。关键在于如何去重，用set的话会超时，应该在固定数字和双指针跳跃的时候都要去重。

```java
class Solution {
    public static List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> ans= new ArrayList();
        Arrays.sort(nums);
        // if(nums.length < 3 || nums[0] > 0 || nums[nums.length - 1] < 0) return ans;
        int len=nums.length;
        int fixed=0;
        while(fixed<=len-3&&nums[fixed]<=0){
            int lo=fixed+1,hi=len-1;
            while(lo<hi){
                if(nums[lo]+nums[hi]+nums[fixed]<0){
                    lo++;
                }else if(nums[lo]+nums[hi]+nums[fixed]>0){
                    hi--;
                }else{
                    List<Integer> tmp=new ArrayList<>();
                    tmp.add(nums[fixed]);tmp.add(nums[lo]);tmp.add(nums[hi]);ans.add(tmp);
                    int t1=lo;while(lo<hi&&nums[lo]==nums[t1])lo++;
                    int t2=hi;while(lo<hi&&nums[hi]==nums[t2])hi--;
                }
            }
            int t=fixed;while(fixed<=len-3&&nums[fixed]==nums[t])fixed++;
        }
        return ans;
    }
}
```

## 岛屿的最大面积
给定一个包含了一些 0 和 1的非空二维数组 grid , 一个 岛屿 是由四个方向 (水平或垂直) 的 1 (代表土地) 构成的组合。你可以假设二维矩阵的四个边缘都被水包围着。

找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为0。)

####   一 二叉树

##### **一 基本二叉树**

###### 1 二叉树 前序和中序的非递归便利

```java
    public static void preOrderUnRecur(Node head) {
        //1先将头结点压入栈
        //2 弹出栈顶元素，再将该节点的左右节点压入栈(先右后左)
        //3 不断重复2 ，直到stack为空
        if (head == null)
            return;
        Stack<Node> stack = new Stack<>();
        stack.push(head);
        while (!stack.isEmpty()) {
            Node cur = stack.pop();
            System.out.println(cur.data + " ");
            if (cur.right != null) {
                stack.push(cur.right);
            }
            if (cur.left != null) {
                stack.push(cur.left);
            }

        }
    }
```





```java
  public static void midOrderUnrecur(Node head) {
        //1 申请一个栈 cur=head
        // 2 将栈左子树依次压入 cur=cur.left
        // 不断重复2，直到cur==null ，弹出node 并打印，并cur=node.right ，继续重复
        if (head == null)
            return;
        Stack<Node> stack = new Stack<>();
        stack.push(head);
        while (!stack.isEmpty() || head != null) {
            if (head!= null) {
                stack.push(head);
                head = head.left;
            }else{
                Node node=stack.pop();
                System.out.println(node.data);
                head=node.right;
            }

        }
    }
```

使用递归方式 不理解

```java
 
    TreeNode prev =null;// 中序遍历这棵树，并保存前驱节点至prev中
    //使用递归
    public boolean isValidBST(TreeNode root){
        if(root != null){
            if(!isValidBST(root.left))
                return false;
            if(prev!=null && root.val<=prev.val){
                return false;
             }
            prev = root;
            return isValidBST(root.right);
        }
        return true;
    }
```



###### 2 二叉树的序列化和反序列化 again

1 使用递归方式

2 使用**层序遍历**方式 层序遍历使用了队列

```java
  /*
     *@description:将二叉树转换为字符串
     * # 表示节点为null ,每个节点以 '!' 分割
     *@params:[head]
     *@return:java.lang.String
     *@author:Mao
     *@date:2019/4/12 8:46
     **/
    public static String seriaByPre(Node head) {
        if (head == null)
            return "#!";
        String res = head.data + "!";
        res += seriaByPre(head.left);
        res += seriaByPre(head.right);
        return res;
    }

    /*
     *@description:反序列化 将str转换为二叉树
     *@params:[str]
     *@return:com.mao.tree.Node
     *@author:Mao
     *@date:2019/4/12 8:53
     **/
    public static Node reconByPre(String str) {
        if (str == null)
            return null;
        //将str 按照'!'分割成数组
        String[] strs = str.split("!");
        Queue<String> queue = new LinkedList<>();
        for (int i = 0; i < strs.length; i++) {
            queue.offer(strs[i]);
        }
        return reconByPreOrder(queue);
    }

    /*
     *@description:前序递归
     *@params:[strs]
     *@return:com.mao.tree.Node
     *@author:Mao
     *@date:2019/4/12 8:59
     **/
    private static Node reconByPreOrder(Queue<String> queue) {
        String value = queue.poll();
        if ("#".equals(value)) {
            return null;
        }
        Node node = new Node(Integer.valueOf(value));
        // 将值与节点的左右子树相连接
        node.left = reconByPreOrder(queue);
        node.right = reconByPreOrder(queue);
        return node;
    }

```

------



```java
  //层序遍历
    public static String seriaByLevel(Node node) {
        if (node == null)
            return "#!";
        String res = node.data + "!";
        Queue<Node> queue = new LinkedList<Node>();
        queue.offer(node);
        while (!queue.isEmpty()) {
            Node head = queue.poll();
            if (head.left != null) {
                res += head.left.data + "!";
                queue.offer(head.left);
            } else {
                res += "#!";
            }
            if (head.right != null) {
                res += head.right.data + "!";
                queue.offer(head.right);
            } else {
                res += "#!";
            }
        }
        return res;
    }

    /*
     *@description:层序遍历反序列化
     *@params:[args]
     *@return:void
     *@author:Mao
     *@date:2019/4/12 9:55
     **/
    public static Node reconByLevel(String str) {
        if (str == null)
            return null;
        String[] strs = str.split("!");
        int index = 0;
        Node head = generatedNode(strs[index++]);
        Queue<Node> queue = new LinkedList<>();
        queue.offer(head);
        Node node = null;
        while (!queue.isEmpty()&& index!=strs.length) {
            node = queue.poll();
            node.left = generatedNode(strs[index++]);
            node.right = generatedNode(strs[index++]);
            if (node.left != null) {
                queue.offer(node.left);
            }
            if (node.right != null) {
                queue.offer(node.right);
            }

        }
        return head;
    }

    private static Node generatedNode(String str) {
        if ("#".equals(str)) {
            return null;
        }
        return new Node(Integer.valueOf(str));
    }
```

###### 3 判断t1树是否全部包括t2树的全部拓扑结构  agiain

注意 ：需检查是否为null

1 先进行 t1树的前序遍历 

2 在与t1 和t2 的每个节点进行比较

```java
   public static boolean contains(Node t1,Node t2){
		//需检查t1 是否为null 为null则停止
        if(t1==null){
            return false;
        }
        return check(t1,t2)|| contains(t1.left,t2)|| contains(t1.right,t2);
    }

    private static boolean check(Node tt1, Node tt2) {
        if(tt2==null){
            return true;
        }
        if(tt1==null || tt2.data!=tt1.data){
            return false;
        }
        return check(tt1.left,tt2.left) && check(tt1.right,tt2.right);
    }
```



##### 二、平衡二叉树



------

- 难度一星

###### 1 通过有序数组生成平衡二叉树

采用递归方式实现

```java
	public Node generateTree(int[] sortArr) {
		if (sortArr == null)
			return null;
		return generate(sortArr, 0, sortArr.length - 1);
	}

	private Node generate(int[] sortArr, int start, int end) {
		if (start > end)
			return null;
		int mid = (start + end) / 2;
		Node head = new Node(sortArr[mid]);
		head.left = generate(sortArr, start, mid - 1);
		head.right = generate(sortArr, mid + 1, end);

		return head;
	}
```



######   2 在二叉树中找到一个节点的后继节点

- 思路一：
- 时间复杂度O(n) 空间复杂度 O(n)
- 1 先找到根结点
- 2 根据根节点就行中序遍历
- 3 遍历结果即为节点的后一个节点



###### 3 二叉树节点间的最大距离问题

 * 找到两个节点最近的祖先
 * 思路：
 * 采用后序遍历
 * 1 如果cur 节点下 左右子树，均无法找到o1 或o2，则返回null
 * 2 若cur 节点，左右子树能找到，则返回cur
 * 3 如果有一个子树返回为null，另一个子树找到，假设不为空的记为node，则存在两种情况
 * 1）则node该节点是O1 或 O2  
 * 2）node是最近的公共祖先
 * 例如遍历节点1 ，发现node节点为3 则  3是o1和o2的父节点



###### 4   先序与中序结合构成重构二叉树   +

 * 时间复杂度： O(n)     p172
 * 在中序数组找位置i过程可以用hash表来实现，复杂度为O(n)
 * 思路：
 * 1 先序数组最左边为树的头结点h，中序找到h，假设位置i。则中序
 *    数组中，i左边数组就是的头结点左子树的中序数组，长度L，则左子树
 *    的先序数组就是先序数组的h+L
 * 2 用左子树先序和中序，递归整个过程建立左子树left
 * 3 用右子树的的先序和中序数组，递归右子树right
 * 4 把head左右孩子设为lef 和  right

```java
   public Node preInToTree(int[] preArr,int[] midArr){
        if(preArr==null || midArr==null){
            return null;
        }
        //使用HashMap将midArr数组存储
        HashMap<Integer,Integer> map=new HashMap<>();
        for (int i=0;i<midArr.length;i++){
            map.put(midArr[i],i);
        }
        return  preTree(preArr,0,preArr.length-1,midArr,0,midArr.length-1,map);
    }
    private Node preTree(int[] preArr, int preStart, int preEnd, int[] midArr, int midStart, int midEnd, Map<Integer,
                Integer> map) {
            if (preStart> preEnd){
                return null;
            }
            Node head=new Node(preArr[preStart]);
            int index=map.get(preArr[preStart]);
            head.left=preTree(preArr,preStart+1,preStart+midEnd-index,midArr,midStart,index-1,map);
            head.right=preTree(preArr,preStart+index-midStart+1,midEnd,midArr,index+1,midEnd,map);
            return head;
    }
```

###### 4.2 中序与后序构成二叉树

```java
  public Node inPosToTree(int[] mid,int[] pos){
        if(mid==null || pos==null){
            return null;
        }
        HashMap<Integer,Integer> map=new HashMap<>();
        for (int i=0;i<mid.length;i++){
            map.put(mid[i],i);
        }
        return posToTree(pos,0,pos.length-1,mid,0,mid.length-1,map);
    }

    private Node posToTree(int[] pos, int posStart, int posEnd, int[] mid, int midStart, int midEnd,
                           HashMap<Integer, Integer> map) {
        if(posStart>posEnd){
            return null;
        }
        int arr=pos[posEnd];
        int index=map.get(arr);
        Node node=new Node(arr);
        node.left=posToTree(pos,posStart,posStart+index-midStart-1,mid,midStart,index-1,map);
        node.right=posToTree(pos,posStart+index-midStart,posEnd-1,mid,index+1,midEnd,map);
        return node;
    }
    public static void main(String[] args) {
        int[] pos=new int[]{4,5,2,6,7,3,1};
        int[] in=new int[]{4,2,5,1,6,3,7};
        InPosToTree test=new InPosToTree();
        Node head=test.inPosToTree(in,pos);
        test.pre(head);
    }
    public void pre(Node head){
        if (head==null)
            return;
        System.out.print(head.data);
        pre(head.left);
        pre(head.right);
    }
```



###### 5 中序与后序结合构成的二叉树

- 时间复杂度： O(n)
- 思路：
- 1 后序数组的最右边为树的头结点h，中序找到h，假设位置为i。
-    则中序数组中，i 左边数组就是头结点左子树的中序数组，长度为L。
-    则左子树的后序数组就是后序数组的h-L
- 2  用左子树的后序和中序，递归建立左子树left
- 3 用右子树的后序和中序，递归建立右子树right
- 4 把head和左右子树设为left和right

###### 6 通过先序和中序数组生成后序数组

  *要求：不要重建整个树，直接生成后序数组*

- ​    1 根据当前的先序和中序，设置后序数组的最右边值
- ​    2 划分出左子树的先序、中序数组；
- ​	右子树的先序、中序数组。
- ​    3 根据右子树划分设置好后序数组，再根据左子树划分



###### 7 判断是否为二叉树  +1

判断是否为平衡二叉树 左右子树高度差不大于1

```java
//boolean res 相当于全局变量
/* 当不满足时立刻返回
	if(!res[0])
	{
      return level;
    }
  */

public static boolean isBanlance(Node head){
        boolean[] res=new boolean[1];
        res[0]=true;
        System.out.println("height:"+getHeight(head,1,res));
        return res[0];
    }

    private static int getHeight(Node head, int level, boolean[] res) {
        if(head==null)
            return level;
       int lh= getHeight(head.left,level+1,res);
        if(!res[0]){
            return level;
        }
        int rh=getHeight(head.right,level+1,res);
        if(!res[0])
            return level;
        if(Math.abs(lh-rh)>1){
            res[0]=false;
        }
        return Math.max(lh,rh);
    }

    public static void main(String[] args) {
        String str = "1!2!#!4!#!#!#!";
        Node head = reconByLevel(str);
        System.out.println( isBanlance(head));
    }
```

###### 8 根据后序数组判断是否为二叉树搜索树

- 根据数组 判断是否为二叉搜索树

  **性质：**依据二叉树后序遍历性质 头结点一定是数组的最后一个元素

  根据平衡二叉树的性质 比头结点大的数组一定在二叉树的右边，

  比头结点小的在二叉树的左边

  **思路：**

   1 声明less 和 more 表示 左右子树的右边界 和 左边界

  2 从start 到 end 开始遍历 找到less 和more

  3 如果 less==-1 或者 more==end 说明 只存在左子树或右子树

  4 若 less==more-1 说明当前左右子树符号条件 分别判断左子树和右子树内部是否满足

  5 终止条件 当 起始位置和终止位置 相等时 则证明符号条件



```java
public class IsPostArray {
    public boolean isPostArray(int[] arrs){
        if(arrs==null ||arrs.length==0)
            return false;
        return  isPost( arrs, 0 , arrs.length-1);
    }
    private boolean isPost(int[] arrs, int start, int end) {

        // 整个递归的终止条件
        if(start==end){
            return true;
        }
        int less=-1; //找到左子树右边界临界点
        int more=end; //找到右子树最左边
        //找到左子树的右边界，右子树的左边界
        for(int i=start;i<end;i++){
            if(arrs[end]>arrs[i]){
                less=i;
            }else {
                //找到第一个比根节点大的索引
                more=more==end?i:more;
            }
        }
        //只存在左子树或只存在右子树
        if(less==-1 || more==end){
            return isPost(arrs,start,end-1);
        }
        //若左边界和右边界-1 相等 则继续遍历，不等直接返回false
        if(less!=more-1){
            return false;
        }
        //进行左子树和右子树的遍历
        return isPost(arrs,start,less)&& isPost(arrs,more,end-1);
    }
}
```



###### 9 根据后序数组重构二叉树



```java

    public Node postArrayToBST(int[] arrs){
        if(arrs==null)
            return null;
        return  posToBST(arrs,0,arrs.length-1);
    }
    private Node posToBST(int[] arrs, int start, int end) {
            if(start>end)
                return null;
            int less=-1;
            int more=end;
            Node node=new Node(arrs[end]);
            for (int i=0;i<arrs.length;i++){
                if(arrs[end]>arrs[i]){
                    less=i;
                }else {
                    more=more==end?i:more;
                }
            }
            node.left=posToBST(arrs,start,less);
            node.right=posToBST(arrs,more,end);
            return node;
    }
```

###### 10  判断一个二叉树是否为完全二叉树

完全二叉树性质 每层节点都得填满 ，运行最后一层右节点为null

思路：

- 1 按照层序遍历

- 2 若当前节点有右子树，无左子树 则 返回false

- 3 若当前节点并不是左右子树都有（有左子树无右子树， 均为左右子树），则后续节点必须为叶子节点

  

```java
    public boolean isCBT(Node head){
          if(head==null)
              return false;
          Queue<Node> queue=new LinkedList<>();
  
          boolean leaf=true;
          Node left=null;
          Node right=null;
  
          queue.offer(head);
          while(!queue.isEmpty()){
              head=queue.poll();
              left=head.left;
              right=head.right;
              
              if(leaf &&(left!=null || right!=null) || (left==null&&right!=null)){
                  return false;
              }
              if(left!=null){
                  queue.offer(left);
              }
              if(right!=null){
                  queue.offer(right);
              }else{
                  leaf=true;
              }
          }
          return true;
      }
```

  

###### 11 判断一个二叉树是否为二叉搜索树

leetcode 98

[Leetcode 参考资料](https://leetcode.com/articles/validate-binary-search-tree/)

思路：进行中序遍历 则搜索二叉树节点值为升序

注意： 搜索二叉树的性质 BST 

​			非递归的中序遍历 

```java
 public boolean isValidBST(TreeNode root) {
          Stack<TreeNode> stack=new Stack();
        double inOrder=-Double.MAX_VALUE;
        while(!stack.isEmpty() || root!=null){
            while(root!=null){
                stack.push(root);
                root=root.left;
            }
            root=stack.pop();
            if(root.val<=inOrder){
                return false;
            }
            inOrder=root.val;
            root=root.right;
        }
        return true;
    }
```

```java
public boolean isValidBST(TreeNode root) {
          Stack<TreeNode> stack=new Stack();
        double inOrder=-Double.MAX_VALUE;
        while(!stack.isEmpty() || root!=null){
           if(root!=null){
                stack.push(root);
                root=root.left;
            }else{
            root=stack.pop();
            if(root.val<=inOrder){
                return false;
            }
            inOrder=root.val;
            root=root.right;
           }
        }
        return true;
    }
```

###### 12 通过有序数组生成平衡搜索二叉树

思路 ： 数组的中间数为二叉树的头结点 ，左侧 为左子树 右侧为右子树

```java
public class GenerateBSTByOrderArr {
    public static Node generateBST(int[] arr){
        if(arr==null){
            return  null;
        }
        return  getBst(arr,0,arr.length-1);   
    }
    /*
    *@description: 使用递归生成平衡搜索二叉树
    *@params:[arr, i, i1]
    *@return:com.mao.tree.Node
    *@author:Mao
    *@date:2019/4/23 8:31
    **/
    private static Node getBst(int[] arr, int start, int end) {
        if(start> end){
            return null;
        }
        int mid=(start+end)/2;
        Node node=new Node(arr[mid]);
        node.left=getBst(arr,start,mid-1);
        node.right=getBst(arr,mid+1,end);
        return node;
    }
```



###### 13 找到两个节点的最近公共祖先节点 +

不懂

使用后序遍历

```java
      //使用后序遍历
        /*
            1
          /  \
         2     3
       / \    / \
      4   5  6   7
                /
               8
         */
    public Node lowestAncestors(Node head,Node o1,Node o2){
        if(head==null || head==o1 || head==o2){
            return head;
        }
        Node left=lowestAncestors(head.left,o1,o2);
        Node right=lowestAncestors(head.right,o1,o2);
        if(left!=null && right!=null){
            return head;
        }
        //若其中有一个为null 不为null记为node 
        //说明node要么是o1 要么是o2 
        //或者 是o1 和o2的最近祖先
        return left!=null?left:right;
    }
```





------



#### 二 字符串

###### 1 判断两个字符是否互为变形

 若两个字符串str1和str2，如果str1和str2出现字符种类一样且每种字符出现次数一样，

 则str1和str2互为变形词。

 如 str1=123     str2=132 返回true

 str1=123   str2=1223 返回false

 思路：

- 字符串转化为char[] ,使用 toCharArray();
- 假设字符串编码在 0到255范围内，用来统计每个字符出现的次数，记为map
- 通过str1确定出现次数
- str2来减少map内字符出现次数，若出现小于0，则不匹配

###### 2  给定一个字符串str，求全部数字串所代表的数字之和

​    要求：1 忽略小数点

​               2 若左侧出现‘-’，当连续为奇数时，则数字为负，相反则为偶数

   例：str="ab-----1111223a------1111c34"

   思路要点：

- 1 采用3个变量 res 结果 num 当前数字 isN 是否为正负,cur表示当前值
- 2 连续数字 num=num*10+( isN ? cur : -cur)
- 3 如何判断正负 ，判断 “-” 之前的是否为 “-”

###### 3 去掉字符串中连续出现k个0的子串如

如 str=“AB00DF” k=2  str="ABDF"

注意： 

   1 记录首次出现位置 start=start==-1 ? i : start 

​	若为-1 ，表示重新开始记录0出现的首次位置

​	若不为-1，则一直保持首次出现位置

   2 ASCII NULL值 是 0



思路要点：

- 记录0出现的次数，以及首次出现位置
- 当当前字符非0时，检查0出现的次数是否满足
- 字符串最后一个未检查，需遍历完后检查最后是否满足

###### 4 判断两个字符串是否互为旋转词

 * 将两个a拼接起来成A，则A的子串就包含旋转词

- 复杂度若为O(n), 则需要kmp算法
- 使用 jdk 自带的 str.indexOf() , 返回某个指定的字符串值在字符串中首次出现的位置,不是则返回-1

###### 5 替换字符串中连续出现的指定字符串

 给定str form to三个字符串，把str中所有from的自串全部替换成to字符串，

 对连续出现的 from部分要求只替换成一个to字符串，返回最终的结果。

思路：

 * 1 将str包含to中的替换为ascii中的0值
 * 2 将非0的字符串开始合并

具体过程：

 1 遍历，判断str的位置和chaf位置是否相等

​     1.1  若相等，继续遍历from后继位置，判断from的长度与匹配长度一致

​          1.1.1 若长度相等则替换为0

​     1.2 若字符不配，则更新match，重新匹配

 2  替换后的str进行转换，拼接

​     2.1  非0直接拼接

​     2.2  若为0，则且前一个位置为非0

**5.1 补充问题**：

给定字符串统计字符串，以及一个整数index，返回cstr的原始字符串上的第index个字符

如 a_1_b_100 第0个位啊 第50位b。

**tip：**

1 获得连续出现字母数量 num = num * 10 + strs[i] - '0'

2 切换连续字母状态 stage=!stage

3 最后一个需单独计算

###### 6  判断字符数组中是否所有字符都只出现过一次

使用hashSet 或者数组 boolean[] iscontain=new boolean[255];

###### 7 字符串的调整与替换

char[] 数组中的空格字符，替换为%20

1  统计非空格字符和字符长度 

2 从后往前遍历，将空格字符替换为 “%20”

###### 8 反转字符串

时间复杂度： 0(N) 空间复杂度o(1)

举例：dog love pig  反转 pig love dog

思路：先将字符整体反转，然后将每个单词反转

提示：

```java
start = i == 0 || chars[i - 1] == ' ' ? i : start;
end = i == chars.length - 1 || chars[i + 1] == ' ' ? i : end;
```

###### 9 反转字符串二

​	给定一个字符串类型的数组chars 和一个整数size，请把大小为size的半区整体移到右边区，右半区整体移到左边

思路：先反转部分，在整体反转

###### 10 括号字符串的有效性和最长有效长度

题目：给定一个字符串str，判断是不是有整体有效的括号字符串

思路：1 从左到右依次遍历字符串，判断每一个字符是不是‘（’ 或‘）’，不是直接返回false

​	    2 依次遍历每一个，检查'(' 和 ')' 的数量，如果‘）’更多则直接返回

​	    3 遍历后检查检查'(' 和 ')' 的数量

思路2： 使用动态规划

###### 11 找到被指的新字符类型

​	ASCII 大写字母 65-90

​	          小写字母 97-122

题目：	 指出新的字符， 小写字母 一个
                                                 大写字母 两个 包括 AA Ab型

  思路1：从前遍历，遍历时，划分 

  思路2 ：从k位置向前遍历,并记录大写字母个数num，直到遇到小写字母停止
 *		若num为奇数，则为 k-1,k
 *		若num为偶数，且k为大写字母，则 (K,K+1)
 *		且K为小写字母,则为 k

#### 三 大数据和空间限制

Hash函数概念介绍：（4个性质）

位运算

###### 1 不用任何额外变量交换两个整数的值？

 如果给定整数a和b，则可以如下设置：

a=a^b;  记录了a和b整数位信息的所有不同信息

b=a^b; 

a=a^b;

###### 2 不用任何比较判断找出两个数中较大的数（四星难度）

题目：给定两个32位整数a和b，返回a和b中较大的

要求：不要做任何比较判断

#### 四 数组

###### 1 转圈打印矩阵

思路：直到起点 (tl,tr) 和终点 (dl,dr) tl++  tr++  dl--  dr--

###### 2 将正方形顺时转到90度 

矩阵颠倒

###### 3 矩阵之字形打印

保证对角线打印 分为上下两个坐标，分别移动 p335

###### 4 需要排序的最短子数组

 * 需要排序的最短子数组长度
 * 从右向左 找出最小的位置minIndex
 * 从左向右 记录最大值，以及maxIndex
 * 范围为(minIndex,maxIndex)

###### 5 在行列都排好序的矩阵中找数

 * 思路：从右上角开始找 目标k
 * 若当前值小于K 则行row+1
 * 若当前值大于K，则列col-1
   

###### 6 子数组累加和

arr=[1,-2,3,5,-2,6,-1] ，则 [3,5,-2,6]累加最大值为12

累加称为负数清零，重新累加

###### 7 不包含本位置的累乘数组

 * **思路一：**
 * 使用除法，重点在零的个数
 * 当非零，则除当前数
 * 当一个零，则，非零位置，均为零，零位置为其他乘积
 * 当大于一个零，则均为零

**思路二：**

不使用除法，分为左右两个组累乘 lr[] 和rl[]

若不能申请额外空间，则复用res[]

###### 8 数组的partiotion调整（重点 以经第二遍）P382

[资料](https://blog.csdn.net/u013309870/article/details/70172830)

分为左区域无重复区域 右侧重复区域

当当前值不等于左侧区域最后一个数时，将当前值放大左侧区域

**还有很多变种问题**

如： 给定一个数组arr，其中包含0,1,2 实现arr排序

有一个数组，其中有红、蓝黄 ，红色左边蓝色中间黄色右边

#### 五 其他问题

###### 1 设计一个setAll函数的HashMap （泛型理解不深刻）

put get isContain setAll 要求时间复杂度都为O(1)

需要考虑泛型的使用方法，泛型类和泛型方法 （泛型数组？）

思路，设计时间戳

###### 2  从N个数中等概论打印M个数  

  题目： 给定一个长度为N且没有重复元素的数组arr和一个整数n，实现函数等概论随机打印arr中的M个数
  时间复杂度o(m) 空间复杂度 O(1)
  思路一：使用hash表来记录是否打印
  思路二：与最后一个元素位置交换


###### 3  判断一个数是否是回文数

给定一个32位整数，判断是否是回文数

如 121 22 -121 -22

注意：32位整数最小值为 -2147483648 绝对值会产生溢出

"/" 获得高位  "%"获得低位

#### 六 栈和队列

###### 1  设计一个有getMin功能的栈  （again）

 * 题目： 实现一个特殊的栈，在栈的基础功能上，返回栈中最小元素
 * 思路：使用两个栈，一个用来存储数据，一个用来存储当前最小值
 * 需要考虑栈入栈和出栈规则
 * 1 入栈 stack_min 中与栈顶比较，若比当前小或者相等则入栈，否则不变
 * 2 出栈时，stackMin元素总是小于等stackData栈顶元素 相等则都出，min小于data则只出data

###### 2   猫狗队列 (again)

 * add
 * pollAll
 * pollDog
 * pollCat
 * isEmpty
 * 思路： 使用时间戳 添加PetEnter类  最后在CatDogQueue内实现

###### 3 用一个栈实现另一个栈

题目： 一个栈中元素的类型为整型，现将该栈从顶到底按从大到小顺序排序，只允许申请一个额外栈。可申请额外变量，但不能申请额外数据结构，如何排序。

思路：排序栈为stack 申请辅助栈为help stack上执行pop，弹出元素为pop

​	1 若cur小于 或等于 help的栈顶元素，直接压入help

​	2 若大于 则将help元素逐一弹出，逐一压入stack，直到cur <= help 的栈顶元素

```java
	while (!s1.isEmpty()) {
			int tmp = s1.pop();
        	// 此处不能有等于 会无限循环
			while (!s2.isEmpty() && tmp < s2.peek()) {
				s1.push(s2.pop());
			}
			s2.push(tmp);
		}
```

#### 七 链表问题

###### 1 打印两个有序链表的公共部分

题目：给定两个有序链表的头指针head1和head2 ，打印公共部分

思路 head1 <head2 head1 往下移动 同理

注意：加强如何构建链表，如何打印链表，以及打印完链表指针后移的要素

###### 2  删除倒数第K个节点 （again）

原因：没有考虑边界问题 删除时可以利用之前的K

 * 边界问题 删除倒数K节点是否超出数组范围，以及头结点和其他节点的不同的删除方式
 * 思路： 每移动一次k就减一，根据K是否大于0来判断
 * K>0 证明 超出范围
 * k=0 恰好头结点，删除头结点
 * k<0 则正常情况，删除该节点需获取前一个节点

   

```java
public static Node removeLastKthNode(Node head, int k,int length) {
		if (head == null) {
			return head;
		}
		Node cur = head;
		int k2=k;
		while (cur != null) {
			cur = cur.next;
			k--;
		}
		Node cur2 = head;
		if (k < 0) {
			int tmp=k2-length;
			//直接使用K  while(++k!=o)
			while (tmp++ != 0) {
				cur2 = cur2.next;
			}
			Node next = cur.next;
			cur2.next = next.next;
			next.next = null;
			return head;

		} else
		if (k == 0) {
			head = head.next;
			return head;
		} else {
			return head;
		}
```

###### 3 删除链表的中间节点 (again) 

* 进阶问题 a/b处的节点（后序完成）

 * 原因：如何删除前一个节点 让h2开始就从头节点移动两次（比h1多移动一次） 此时h1就是前一个节点
 * 思路：申请两个节点h1,h2 h1每次只移动一次，h2移动两次
 * 当h2的下一次为null 则返回当前h1

```java
public static Node removeMiddleNode(Node node) {
		if (node == null) {
			return node;
		}
		if (node.next.next == null) {
			return node.next;
		}
		Node pre = node;
		Node cur = node.next.next;
		while (cur.next != null && cur.next.next != null) {

			pre = pre.next;
			cur = cur.next.next;

		}
		pre.next = pre.next.next;
		return node;
	}
```

###### 4 反转单向链表和双向链表 (again)

注意：还可以使用递归方式 

<https://blog.csdn.net/wxm349810930/article/details/46724691>

思路：将头结点从链表拆除，然后对剩余节点逆序，最后将表头节点连接到新链表尾部

4.1 反转单向链表

 * 思路：声明两个节点next和pre 

 * next 保存头结点后面的节点

 * pre 保存前一个节点

 * head.next=pre //此事head指向pre 已经和原先节点断链

 * pre=head  //更新pre节点

 * head=next //将保存后序节点在重新给head

 * 最后pre就是头结点head此时为空

 * ```java
   public static Node reverse(Node head) {
   		Node next = null;
   		Node pre = null;
   		while (head != null) {
   			next = head.next;// 保存头结点后面的节点
   			head.next = pre; // 此时head指向pre 已经和原先节点断链
   			pre = head;
   			head = next;// 将保存后序节点在重新给head
   
   		}
   		// 返回pre而不是head？
   		return pre;
   	}
   ```

   

4.2 反转双向链表

```java
public static DoubleNode reverseDouble(DoubleNode node) {
		DoubleNode next = null;
		DoubleNode pre = null;
		while (node != null) {
			next = node.next;
			node.next = pre;
			node.pre = next;
			pre = node;
			node = next;
		}		
		return pre;
```

###### 5 反转部分链表 （again agian）

难点：如何获得要反转部分的前一个及最后一个，以及如何正确连接节点

思路：

1 先获得要反转节点from的前一个 preFrom 和反转to的后一个postTo

2  先将反转的第from位置节点和postTo连接 （注意此时断链，需要声明一个节点保存后序信息）

3 反转部分节点

4 若preFrom为null 则是反转了头结点，若反转不为空，返回新节点，

（此时head节点已不是头结点 而是指向了postTo位置节点

```java
	public static Node partReverse(Node head, int from, int to) {
		// 找到要反转节点的前一个节点，且不是头结点
		int i = 0;
		Node h1 = head;
		Node preFrom = null;
		Node postTo = null;
		// 获得链表from前一个preFrom.to的后一个postTo
		while (h1 != null) {
			i++;
			preFrom = i == from - 1 ? h1 : preFrom;
			postTo = i == to + 1 ? h1 : postTo;
			h1 = h1.next;
		}		
		//拿到要反转的第一个节点
		h1 = preFrom == null ? head : preFrom.next;	
		//连接翻转后的尾节点	
		Node h2 = h1.next;
		h1.next = postTo;
		// 反转节点
		Node next = null;
		int j = to - from;
		while (h2 != postTo) {
			next = h2.next;
			h2.next = h1;
			h1 = h2;
			h2 = next;
		}
		// h1.next = pre;
		if (preFrom != null) {
			preFrom.next = h1;
			// 返回旧节点
			return head;
		}
		// 返回新节点
		return h1;
	}
```

###### 6  环形单链表约瑟夫问题 (again)

注意：掌握循环链表，如何找到最后一个节点 注意循环链表的判断条件

 * 单向环形链表

 * 每m个就删除当前节点，并重新计数

 * 直到剩下最后一个

    ```java
    public static Node jsoepuseKill(Node head, int m) {
    		if (head == null || head.next == head || m < 1)
    			return head;
    		// 找到last节点
    		Node last = head;
    		while (last.next != head) {
    			last = last.next;
    		}
    
    		int count = 0;
    		while (head != last) {
    			if (++count == m) {
    				last.next = head.next;
    				count = 0;
    			} else {
    				last = last.next;
    			}
    			head = head.next;
    		}
    		return head;
    
    	}
    ```

    

###### 7 判断是否为回文结构

 * 注意：思路二中移动时 Node cur = head.next; 

   ​            在移动后cur就是右链的第一个节点（这块出错）

 * 思路1 ：使用栈（没想到），入栈顺序和出栈顺序一致

 * 思路2：同样使用栈，只讲链表的一半压入栈，比较左链和右链是否相等

   ​	      需要进行奇数偶数判断。

###### 8 两个链表相加

注意： 1 需考虑当两个栈都为空，且还存在进位时

​	    2 如何更新节点

​	    3 相加时精简写法

```java
    val1=s1.isEmpty()?0:s1.pop();
    val2=2.isEmpty()?0:s2.pop();
	val3=val1+val2+cal;
	cal=val3/10; //进位
    pre=cur;
	cur=new Node(cal%10);
	cur.next=pre;
```

​		

```java
     	pre = cur;
		cur = new Node(val3);
		cur.next = pre;
```
 * 思路1：加完在生成链表 ，有可能相加时会存在溢出
 * 思路2：利用栈，需考虑进位

```java
public static Node addList1(Node head1, Node head2) {
		if (head1 == null || head2 == null) {
			return head1 == null ? head1 : head2;
		}
		Stack<Integer> s1 = new Stack<Integer>();
		Stack<Integer> s2 = new Stack<Integer>();
		while (head1 != null || head2 != null) {
			if (head1 != null) {
				s1.push(head1.data);
				head1 = head1.next;
			}
			if (head2 != null) {
				s2.push(head2.data);
				head2 = head2.next;
			}
		}
		Node pre = null;
		Node cur = null;
		int val1 = 0;
		int val2 = 0;
		int cal = 0;
		while (!s1.isEmpty() || !s2.isEmpty()) {

			if(!s1.isEmpty()){
				val1 = s1.pop();
			} else {
				val1 = 0;
			}
			if(!s2.isEmpty()){
				val2=s2.pop();
			} else {
				val2 = 0;
			}

			int val3 = val1 + val2 + cal;
			if (val3 >= 10) {
				cal = 1;
				val3 = val3 % 10;
			} else {
				cal = 0;
			}
			pre = cur;
			cur = new Node(val3);
			cur.next = pre;
		}
		// 当两个栈都为空，且还存在进位时
		if (cal == 1) {
			Node n = new Node(cal);
			n.next = cur;
			return n;
		}

		return cur;
	}
```

###### 9 删除重复节点 （again 思路二未实现）

 * 难点：思路1中推荐使用hashset
 * 思路1 使用hashMap 时间复杂度O(n) 空间复杂度O(n)
 * 思路2 使用选择排序 时间复杂度O(n2) 空间复杂度O(1)

```java
public static Node removeRepeat(Node head) {
		if(head==null)
			return null;
		HashMap<Integer, Integer> map=new HashMap<Integer, Integer>();
		map.put(head.data, 1);
		Node n1 = head;
		Node pre = head;
		Node cur = head.next;
		while (cur != null) {
			if (map.containsKey(cur.data)) {
				pre.next = cur.next;
				cur = cur.next;

			} else {
				map.put(cur.data, 1);
				pre = cur;
				cur = cur.next;
			}


		}
		return n1;
	}

```

###### 10 删除指定节点

难点 ：思路二连接链表存在更简单做法

```java
 stack.peek().next=head;
 head=stack.pop();
```

 * 思路1 直接删除 时间复杂度O(N) 空间复杂度O(1)
 * 思路2 借助容器 将值不等的节点收集起来然后重新连接（没想到）

```java
//直接删除
public static Node removeSpecialValue(Node head, int num) {
		if(head==null)
			return head;
		Node h = head;
		Node pre = head;
		Node cur = head.next;
		while (cur != null) {
			if (cur.data == num) {
				pre.next = cur.next;
			} else {
				pre = pre.next;
			}
			cur = cur.next;
		}
		return h;
	}
```

```java

//使用栈
public static Node removeSpecialValueByStack(Node head, int num) {
		Stack<Node> s = new Stack<Node>();
		while (head != null) {
			if (head.data != num) {
				s.push(head);
			}
			head = head.next;
		}
		Node pre = null;
		Node cur = null;
		while (!s.isEmpty()) {
			cur = s.pop();
			cur.next = pre;
			pre = cur;
		}	
		return cur;
	}
```

###### 11 实现单链表的选择排序

要求：额外空间复杂度O(1)

选择排序要点：从未排序的部分中找到最小值，然后放在排好序的尾部

- 获取最小节点的前一个节点

```java
// 获得最小节点的前一个节点
	private static Node getSmallPre(Node head) {
		Node smallPre = null;
		Node small = head;
		Node pre = head;
		Node cur = head.next;

		while (cur != null) {
			if(cur.data<pre.data){
				smallPre=pre;
				small = cur;
			}
			pre = cur;
			cur = cur.next;

		}
		return smallPre;
	}
```



- 使用链表进行选择排序

  ```java
  	public static Node selectionSort(Node head) {
  		if (head == null) {
  			return null;
  		}
  		Node tail = null; //排序部分的尾部
  		Node cur=head; //未排序的尾部
  		Node smallPre = null; // 最小节点的前一个节点
  		Node small=null; //最小节点
  		while (cur != null) {
  			small = cur;
  			// 找到最小节点
  			smallPre = getSmallPre(cur);
  			if (smallPre != null) {
  				small = smallPre.next;
  				smallPre.next = small.next;
  			}
  			// cur==small 则cur节点就是最小节点，然后继续遍历
  			// cur!=small 则cur节点不是，需继续从cur遍历找到整个未排序部分的最小节点
  			cur = small == cur ? cur.next : cur;
  			if (tail == null) {
  				tail = small;
  			} else {
  				tail.next = small;
  
  			}
  			tail = small;
  		}
  		return head;
  	}
  ```

  ###### 12 一种诡异的删除节点方式

  只给定一个节点node ，不给的头结点 要求删除该节点

  当该节点为null，不能使用复制方式，无法删除

  ```java
  public static void removeNodeWired(Node node) {
  		if (node == null) {
  			return;
  		}
  		if (node.next == null) {
  			throw new RuntimeException("无法删除");
  		}
  		//把node.next的节点复制给node 删除node.next
  		node.data = node.next.data;
  		node.next = node.next.next;
  
  	}
  ```

  

###### 13 向有序的环形单链表中插入新节点

- 特殊情况 当num 遍历完后 ，没有找到插入的地方，会有两种情况

1 该节点比头结点小

2该节点比头结点大

- 在环形链表中遍历的 cur ！=head
- pre=head  cur=head.next;
- 

```java
//经典实现
public static Node insertByCircle2(Node head, Node num) {
		if (head == null) {
			num.next = num;
			return num;
		}

		Node pre = head;
		Node cur = head.next;
		while (cur != head) {
			if (pre.data <= num.data && num.data <= cur.data) {
				break;
			}
			pre = cur;
			cur = cur.next;
		}
		pre.next = num;
		num.next = cur;
		return head.data > num.data ? num : head;
	}
```

```java
//自己实现
public static Node insertByCircle(Node head, Node num) {
		if (head == null)
			return null;

		
		Node cur = head;
		Node tail = null;
		// 获取taile节点
		while (cur.next != head) {
			cur = cur.next;
		}
		tail = cur;
		cur = tail.next;
		while (cur != tail) {
			if (cur.data >= num.data && cur.next.data <= num.data) {
				//插入节点
				Node next=cur.next;
				cur.next=num;
				num.next=next.next;
						
			}
			cur = cur.next;

		}
		if (num.data < head.data ) {
			tail.next = num;
			num.next = head;
			head = num;
		}
		if (num.data > tail.data) {
			tail.next=num;
			num.next=head;
			tail = num;
		}
		return head;
	}
```

###### 14 合并两个有序链表 （again）

```java
public static Node merge(Node head1, Node head2) {
		if (head1 == null || head2 == null) {
			return head1 == null ? head2 : head1;
		}
		// 头结点为两个中最小的一个
		Node head = head1.data < head2.data ? head1 : head2;
		Node cur1 = head == head1 ? head1 : head2;
		Node cur2 = head == head1 ? head2 : head1;
		Node pre=null;
    	//书上 Node next=null;
		while (cur1 != null && cur2 != null) {
			if(cur1.data<cur2.data){
				pre=cur1;
				cur1 = cur1.next;
			} else {
				Node next = cur2.next;
				pre.next = cur2;
				cur2.next = cur1;
				cur2 = next;
				pre = cur2;
			}
		}

		pre.next = cur1 == null ? cur2 : cur1;
		return head;

	}
```

###### 15  左右半区方式重新组合链表

思路：1首先要划分出左右半区 需要声明一个mid变量

​	    2  组合左右两半区

```java
  public static Node relocate(Node head){
        if(head==null)
            return null;
        //将链表划分左右两半区
        Node mid=head;
        Node right=head.next;
        while(right.next!=null && right.next.next!=null){
            mid=mid.next;
            right=right.next.next;
        }
        System.out.println("node:"+mid.next.data);
        Node cur=head;
        right=mid.next;
      // 标准写法 
      // mid.next=null;
      //while(cur.next!=null)
        while(cur!=mid){
            Node next_left=cur.next;
            Node next_right=right.next;
            cur.next=right;
            right.next=next_left;
            right=next_right;
            cur=next_left;
        }
        cur.next=right;
        return head;
    }
```


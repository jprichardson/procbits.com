<!--
author: JP
publish: Mon Aug 09 2010 15:14:19 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/08/09/traversal-of-gwt-tree/
tags: GWT
slug: 2010/08/09/traversal-of-gwt-tree
-->

Traversal of GWT Tree
=====================

Recently, an app I'm working on needed two trees. I just so happened
that I needed to deep copy part of one of the trees to the other tree.
The copied tree will then have checkboxes as nodes instead of just
Strings.

So how would you do this? The first solution that came to mine is
depth-first traversal of the tree. This method could be adapted to any
tree structure in just about any language.

We need a callback function for everytime we 'hit' a tree node. Let's
create an interface called 'Action': Action.java:

```java
public interface Action<T> {
    public void execute(T object);
}
```

Now, we need to create a file called TreeItemUtil.java. This will
include the algorithm. The algorithm is really straightforward, it just
uses a stack for each node and iterates the stack. If a node has
children, they are pushed onto the stack. TreeItemUtil.java:

```java
public class TreeItemUtil {
    
    public static Collection<TreeItem> fetchChildren(TreeItem ti){
        List<TreeItem> children = new ArrayList<TreeItem>();
        
        for (int i = 0; i < ti.getChildCount(); ++i)
            children.add(ti.getChild(i));
        return children;
    }
    
    public static void traverseDfs(TreeItem root, Action<TreeItem> onNode){
        if (onNode == null)
            throw new IllegalArgumentException("Must pass an action.");
        
        Stack<TreeItem> children = new Stack<TreeItem>();
        children.add(root);
        
        while (!children.isEmpty()){
            TreeItem current = children.pop();
            onNode.execute(current);
            List<TreeItem> currentChildren = fetchChildren(current);
            children.addAll(currentChildren);
        }
    }
}
```

Here is our TreeItemUtilTest.java that uses Mockito:

```java
public void testTraverseDfs(){
        final StringBuffer sb = new StringBuffer();
        
        TreeItem a = createMockedTI("a");
        TreeItem b = createMockedTI("b");
        TreeItem c = createMockedTI("c");
        TreeItem d = createMockedTI("d");
        TreeItem e = createMockedTI("e");
        TreeItem f = createMockedTI("f");
        TreeItem g = createMockedTI("g");
        TreeItem h = createMockedTI("h");
        TreeItem i = createMockedTI("i");
        TreeItem j = createMockedTI("j");
        TreeItem k = createMockedTI("k");
        TreeItem l = createMockedTI("l");
        TreeItem m = createMockedTI("m");
        TreeItem n = createMockedTI("n");
        TreeItem o = createMockedTI("o");
        TreeItem p = createMockedTI("p");
        
        addMockedChildren(a, b, o);
        addMockedChildren(b, c, i);
        addMockedChildren(o, n, p);
        addMockedChildren(c, d);
        addMockedChildren(i, j, m);
        addMockedChildren(d, e);
        addMockedChildren(j, k, l);
        addMockedChildren(e, f, g);
        addMockedChildren(g, h);
        
        TreeItemUtil.traverseDfs(a, new Action<TreeItem>(){
            public void execute(TreeItem current){
                sb.append(current.getText());
            }
        });
        
        //I was assuming it would be leftmost child first, if so, it would be in alphabetical order
        //but it starts with rightmost node first.
        assertEquals("aopnbimjlkcdeghf", sb.toString());
    }
    
    private TreeItem createMockedTI(String text){
        TreeItem ti = mock(TreeItem.class);
        when(ti.getText()).thenReturn(text);
        return ti;
    }
    
    private void addMockedChildren(TreeItem parent, TreeItem... children){
        for (int x = 0; x < children.length; ++x){
            when(parent.getChild(x)).thenReturn(children[x]);
            when(children[x].getParentItem()).thenReturn(parent);
        }
        
        when(parent.getChildCount()).thenReturn(children.length);
    }
```

Now for the actual algorithm that clones the TreeItem into a TreeItem
with checkboxes:

```java
private TreeItem cloneTreeItemsWithCheckboxes(TreeItem root){
        final HashMap<TreeItem, TreeItem> oldNew = new HashMap<TreeItem, TreeItem>();
        
        TreeItemUtil.traverseDfs(root, new Action<TreeItem>(){
            public void execute(TreeItem current){
                TreeItem newTi = new TreeItem(new CheckBox(current.getText()));
                newTi.setUserObject(current.getUserObject());
                oldNew.put(current, newTi);
                
                if (current.getParentItem() != null && oldNew.containsKey(current.getParentItem())){
                    TreeItem newParent = oldNew.get(current.getParentItem());
                    newParent.addItem(newTi);
                }
            }
        });
        
        return oldNew.get(root);
    }
```

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP

Introduction to Merkle Tree :       

Merkle Tree
       Merkle tree is a tree data structure with leaf nodes and non leaf nodes. It also known as Hash tree. The reason behind it is it only stores the hashes in its nodes instead of data. In its leaf nodes, it will store the hash of the data. Non leaf nodes contain the hash of its children
Hash Functions
       A hash or hash-value or a message digest is an array of fixed size random characters  generated when a message  or data is passed through an a  Mathematical algorithm. 
These mathematical algorithms are one way functions meaning, we can generate the output from input but not vice-versa. It has to be deterministic, meaning the input should always maps to same output regardless of the number of times it passed through it.  
Y(I) = O, where
Y  = Our hash function
I    = Input 
O  =  Output. 
Even a small change in the input produces completely output. This property is also known as avalanche effect.
Y(I') = O' 
These mathematical algorithms with the above properties are also known as Hash Functions.

Architecture of Merkle Tree
 

Merkle Tree Implementation in Java:
In this example implementation, We are going to implement binary merkle tree. As the first step, let's define the node. Like a regular tree, it has a  data field to store the hash and left and right pointers to point to left  child and right child of the binary tree.

<img width="449" alt="image" src="https://user-images.githubusercontent.com/79223934/165556830-ea724975-623a-4f98-803c-ca1a5823481a.png">


In this example implementation, We are going to implement binary merkle tree. As the first step, let's define the node. Like a regular tree, it has a  data field to store the hash and left and right pointers to point to left  child and right child of the binary tree. 


    public class Node {

    private Node left;
    private Node right;
    private String hash;

    public Node(Node left, Node right, String hash) {
        this.left = left;
        this.right = right;
        this.hash = hash;
    }

    public Node getLeft() {
        return left;
    }

    public void setLeft(Node left) {
        this.left = left;
    }

    public Node getRight() {
        return right;
    }

    public void setRight(Node right) {
        this.right = right;
    }

    public String getHash() {
        return hash;
    }

    public void setHash(String hash) {
        this.hash = hash;
    } 
    }
    
We going to use the below dependency for the java implementations of the cryptography algorithms.  However, we are going to use keccak 256 for our implementation.


        <!--The Bouncy Castle Crypto package is a Java implementation of cryptographic algorithms -->
        <dependency>
            <groupId>org.bouncycastle</groupId>
            <artifactId>bcprov-jdk15on</artifactId>
            <version>1.68</version>
        </dependency>
        
        
Keccak 256 is part of SHA 3 (Secure Hash Algorithm) standard.

    import org.bouncycastle.jcajce.provider.digest.Keccak;
    import org.bouncycastle.util.encoders.Hex;

    import java.nio.charset.StandardCharsets;

    public class HashAlgorithm {

    public static String generateHash(String originalString) {
        Keccak.Digest256 digest256 = new Keccak.Digest256();
        byte[] hashedByteArray = digest256.digest(
                originalString.getBytes(StandardCharsets.UTF_8));
        return new String(Hex.encode(hashedByteArray));
    }
    }
    
We are going to implement a complete binary tree. In case of odd nodes, we will consider the odd node twice to generate the hash of its parent.  
Code:
        
      package com.merkle.tree.implementation;    
      import java.util.ArrayList;    
      import java.util.LinkedList;    
      import java.util.Queue;       
      public class MerkleTree {

      public static Node generateTree(ArrayList<String> dataBlocks) {
        ArrayList<Node> childNodes = new ArrayList<>();

        for (String message : dataBlocks) {
            childNodes.add(new Node(null, null, HashAlgorithm.generateHash(message)));
        }

        return buildTree(childNodes);
    }

    private static Node buildTree(ArrayList<Node> children) {
        ArrayList<Node> parents = new ArrayList<>();

        while (children.size() != 1) {
            int index = 0, length = children.size();
            while (index < length) {
                Node leftChild = children.get(index);
                Node rightChild = null;

                if ((index + 1) < length) {
                    rightChild = children.get(index + 1);
                } else {
                    rightChild = new Node(null, null, leftChild.getHash());
                }

                String parentHash = HashAlgorithm.generateHash(leftChild.getHash() + rightChild.getHash());
                parents.add(new Node(leftChild, rightChild, parentHash));
                index += 2;
            }
            children = parents;
            parents = new ArrayList<>();
        }
        return children.get(0);
    }

    private static void printLevelOrderTraversal(Node root) {
        if (root == null) {
            return;
        }

        if ((root.getLeft() == null && root.getRight() == null)) {
            System.out.println(root.getHash());
        }
        Queue<Node> queue = new LinkedList<>();
        queue.add(root);
        queue.add(null);

        while (!queue.isEmpty()) {
            Node node = queue.poll();
            if (node != null) {
                System.out.println(node.getHash());
            } else {
                System.out.println();
                if (!queue.isEmpty()) {
                    queue.add(null);
                }
            }

            if (node != null && node.getLeft() != null) {
                queue.add(node.getLeft());
            }

            if (node != null && node.getRight() != null) {
                queue.add(node.getRight());
            }

        }

    }

    public static void main(String[] args) {
        ArrayList<String> dataBlocks = new ArrayList<>();
        dataBlocks.add("Captain America");
        dataBlocks.add("Iron Man");
        dataBlocks.add("God of thunder");
        dataBlocks.add("Doctor strange");
        Node root = generateTree(dataBlocks);
        printLevelOrderTraversal(root); 
        }
    }



Reference:
 https://www.pranaybathini.com/2021/05/merkle-tree.html

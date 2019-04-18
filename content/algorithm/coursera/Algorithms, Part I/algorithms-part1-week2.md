---
title: 'Week 2 編程作業:Deques and Randomized Queues'
date: 2019-04-18
weight: 10
tags: ["coursera","Algorithms, Part I","algorithm","stack","queue"]
---



## 編程作業: Deques and Randomized Queues

Deque使用節點，RandomizedQueue使用陣列，100分沒有太困難



### Deque.java

```java
import java.util.Iterator;

public class Deque<Item> implements Iterable<Item> {

	private final Node head;
	private final Node tail;
	private int size;

	public Deque() {
		this.head = new Node();
		this.tail = new Node();
		this.head.next = this.tail;
		this.tail.pre = this.head;
	}

	public boolean isEmpty() {
		return this.size == 0;
	}

	public int size() {
		return this.size;
	}

	public void addFirst(Item item) {
		checkNullItem(item);
		Node newNode = new Node(item);
		insertAfter(this.head, newNode);
	}

	public void addLast(Item item) {
		checkNullItem(item);
		Node newNode = new Node(item);
		insertAfter(this.tail.pre, newNode);
	}

	private void insertAfter(Node a, Node insert) {
		this.size++;
		Node b = a.next;
		insert.next = b;
		insert.pre = a;
		a.next = insert;
		b.pre = insert;
	}

	private static void checkNullItem(Object item) {
		if (item == null)
			throw new java.lang.IllegalArgumentException();
	}

	public Item removeFirst() {
		if (this.size == 0)
			throw new java.util.NoSuchElementException();

		return remove(this.head.next);
	}

	public Item removeLast() {
		if (this.size == 0)
			throw new java.util.NoSuchElementException();

		return remove(this.tail.pre);
	}

	private Item remove(Node n) {
		this.size--;
		Node a = n.pre;
		Node b = n.next;
		n.next = null;
		n.pre = null;
		a.next = b;
		b.pre = a;
		return n.item;
	}

	public Iterator<Item> iterator() {
		return new DequeIterator();
	}

	private class DequeIterator implements Iterator<Item> {

		private Node cur = head;

		@Override
		public boolean hasNext() {
			return cur.next != tail;
		}

		@Override
		public Item next() {
			if (!this.hasNext())
				throw new java.util.NoSuchElementException();

			cur = cur.next;
			return cur.item;
		}

	}

	private class Node {
		Item item;
		Node next;
		Node pre;

		Node() {
		}

		Node(Item item) {
			this.item = item;
		}
	}

	public static void main(String[] args) {
		Deque<Integer> dq = new Deque<>();

		dq.addFirst(1);
		dq.addFirst(2);
		dq.addLast(3);

		System.out.println(dq.removeLast());
		System.out.println(dq.removeLast());
		System.out.println(dq.removeLast());

	}

}
```



### RandomizedQueue.java

```java
import java.util.Iterator;

import edu.princeton.cs.algs4.StdRandom;

public class RandomizedQueue<Item> implements Iterable<Item> {

	private static final int MIN_SIZE = 16;
	private Item[] ar;
	private int size;

	public RandomizedQueue() {
		this.ar = (Item[]) new Object[MIN_SIZE];
	}

	public boolean isEmpty() {
		return this.size == 0;
	}

	public int size() {
		return this.size;
	}

	public void enqueue(Item item) {
		if (item == null)
			throw new java.lang.IllegalArgumentException();
		sizeup();
		this.ar[size++] = item;
	}

	public Item dequeue() {
		if (this.size == 0)
			throw new java.util.NoSuchElementException();

		int i = StdRandom.uniform(0, size);
		Item rtn = this.ar[i];
		this.ar[i] = this.ar[size - 1];
		this.ar[size - 1] = null;
		size--;
		sizedown();
		return rtn;
	}

	private void sizeup() {
		if (this.size < this.ar.length)
			return;

		Item[] newar = (Item[]) new Object[this.ar.length * 2];
		System.arraycopy(this.ar, 0, newar, 0, this.size);
		this.ar = newar;
	}

	private void sizedown() {
		if (this.ar.length == MIN_SIZE || this.size >= this.ar.length / 4)
			return;

		Item[] newar = (Item[]) new Object[this.ar.length / 2];
		System.arraycopy(this.ar, 0, newar, 0, this.size);
		this.ar = newar;
	}

	public Item sample() {
		if (this.size == 0)
			throw new java.util.NoSuchElementException();

		int i = StdRandom.uniform(0, size);
		return this.ar[i];
	}

	public Iterator<Item> iterator() {
		return new RQIterator();
	}

	private class RQIterator implements Iterator<Item> {

		private final int[] is = StdRandom.permutation(size);
		private int i = 0;

		@Override
		public boolean hasNext() {
			return i < size;
		}

		@Override
		public Item next() {
			if (!this.hasNext())
				throw new java.util.NoSuchElementException();

			return ar[is[i++]];
		}

	}

	public static void main(String[] args) {
		RandomizedQueue<Integer> rq = new RandomizedQueue<>();

		for (int i = 0; i < 50; i++)
			rq.enqueue(i);

		for (int i : rq)
			System.out.println(i);

		System.out.println("========");
		for (int i = 0; i < 50; i++)
			System.out.println(rq.dequeue());

	}

}
```



### Permutation.java

```java
import edu.princeton.cs.algs4.StdIn;
import edu.princeton.cs.algs4.StdOut;

public class Permutation {

	public static void main(String[] args) {
		int k = Integer.parseInt(args[0]);
//		int k = 3;

		RandomizedQueue<String> rq = new RandomizedQueue<>();

		while(!StdIn.isEmpty()) {
			String s = StdIn.readString();
			rq.enqueue(s);
		}
		
		for (int i = 0; i < k; i++)
			StdOut.println(rq.dequeue());

	}

}
```
















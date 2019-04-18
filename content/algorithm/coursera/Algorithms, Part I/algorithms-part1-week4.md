---
title: 'Week 4 編程作業:8 Puzzle'
date: 2019-04-18
weight: 30
tags: ["coursera","Algorithms, Part I","algorithm","priority queue","a star"]
---



## 編程作業: 8 Puzzle

只有99分



### Board.java

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Board {

	private final short[] blocks;
	private final int n;
	private int i0;
	private int hamming;
	private int manhattan;
	private long hash;

	public Board(int[][] blocks) {
		this(blocks.length, copy(blocks));
	}

	private Board(int n, short[] blocks) {
		this.blocks = Arrays.copyOf(blocks, blocks.length);
		this.n = n;
		this.hash = 0;

		for (int i = 0; i < blocks.length; i++) {
			if (this.blocks[i] == 0) {
				this.i0 = i;
			} else if (this.blocks[i] != i + 1) {
				this.hash *= 11;
				this.hash += this.blocks[i];
				hamming++;
				int i1 = this.blocks[i] - 1;
				manhattan += Math.abs((i / n) - (i1 / n)) + Math.abs((i % n) - (i1 % n));
			}
		}
	}

	private Board(int n, short[] blocks, int manhattan) {
		this.blocks = Arrays.copyOf(blocks, blocks.length);
		this.n = n;
		this.hash = 0;
		this.manhattan = manhattan;

		for (int i = 0; i < blocks.length; i++) {
			if (this.blocks[i] == 0) {
				this.i0 = i;
			} else if (this.blocks[i] != i + 1) {
				this.hash *= 11;
				this.hash += this.blocks[i];
				hamming++;
			}
		}
	}

	private static short[] copy(int[][] ar) {
		short[] rtn = new short[ar.length * ar.length];
		for (int r = 0; r < ar.length; r++) {
			for (int c = 0; c < ar.length; c++) {
				rtn[rcToIndex(ar.length, r, c)] = (short) ar[r][c];
			}
		}
		return rtn;
	}

	/** r=0~n-1, c=0~n-1, index=0~n^2-1 */
	private static int rcToIndex(int n, int r, int c) {
		return n * r + c;
	}

	/** r=0~n-1, c=0~n-1, i=1~n^2 */
	private static int[] indexToRc(int n, int i) {
		i--;
		return new int[] { i / n, i % n };
	}

	public int dimension() {
		return this.n;
	}

	public int hamming() {
		return hamming;
	}

	public int manhattan() {
		return manhattan;
	}

	public boolean isGoal() {
		return hamming == 0;
	}

	public Board twin() {
		int i1 = i0 >= n ? i0 - n : i0 + n;
		int i2 = i1 % n > 0 ? i1 - 1 : i1 + 1;

		swap(this.blocks, i1, i2);
		Board rtn = new Board(this.n, this.blocks);
		swap(this.blocks, i1, i2);
		return rtn;
	}

	private static void swap(short[] ar, int i, int j) {
		short temp = ar[i];
		ar[i] = ar[j];
		ar[j] = temp;
	}

	public boolean equals(Object y) {
		if (!(y instanceof Board))
			return false;

		Board by = (Board) y;
		if (this.hash != by.hash)
			return false;

		for (int i = 0; i < this.blocks.length; i++) {
			if (this.blocks[i] != by.blocks[i])
				return false;
		}
		return true;
	}

	public Iterable<Board> neighbors() {
		return createNeighbors();
	}

	private List<Board> createNeighbors() {
		List<Board> list = new ArrayList<>(4);

		final int curC = i0 % n;
		final int curR = i0 / n;

		if (i0 >= n) {
			// 空白上移、數字下移
			swap(this.blocks, i0, i0 - n);
//			list.add(new Board(this.n, this.blocks));
			int realR = (this.blocks[i0] - 1) / n;
			list.add(new Board(this.n, this.blocks, this.manhattan + (realR >= curR ? -1 : 1)));
			swap(this.blocks, i0, i0 - n);
		}
		if (i0 < this.blocks.length - n) {
			// 空白下移、數字上移
			swap(this.blocks, i0, i0 + n);
//			list.add(new Board(this.n, this.blocks));
			int realR = (this.blocks[i0] - 1) / n;
			list.add(new Board(this.n, this.blocks, this.manhattan + (realR <= curR ? -1 : 1)));
			swap(this.blocks, i0, i0 + n);
		}
		if (i0 % n > 0) {
			// 空白左移、數字右移
			swap(this.blocks, i0, i0 - 1);
//			list.add(new Board(this.n, this.blocks));
			int realC = (this.blocks[i0] - 1) % n;
			list.add(new Board(this.n, this.blocks, this.manhattan + (realC >= curC ? -1 : 1)));
			swap(this.blocks, i0, i0 - 1);
		}
		if (i0 % n < n - 1) {
			// 空白右移、數字左移
			swap(this.blocks, i0, i0 + 1);
//			list.add(new Board(this.n, this.blocks));
			int realC = (this.blocks[i0] - 1) % n;
			list.add(new Board(this.n, this.blocks, this.manhattan + (realC <= curC ? -1 : 1)));
			swap(this.blocks, i0, i0 + 1);
		}
		return list;
	}

	public String toString() {
		StringBuilder s = new StringBuilder();
		s.append(n + "\n");
		for (int i = 0; i < this.blocks.length; i++) {
			s.append(String.format("%2d ", blocks[i]));
			if (i % n == n - 1)
				s.append("\n");
		}
		return s.toString();
	}

}
```



### Solver.java

```java
import java.util.LinkedList;

import edu.princeton.cs.algs4.MinPQ;

public class Solver {

	private final Node goal;

	public Solver(Board initial) {
		if (initial == null)
			throw new java.lang.IllegalArgumentException();

		this.goal = aStar(initial);
	}

	private static Node aStar(Board initBoard) {
		MinPQ<Node> pq = new MinPQ<>(16);

		pq.insert(new Node(null, initBoard, 0, true));
		pq.insert(new Node(null, initBoard.twin(), 0, false));

		while (!pq.isEmpty()) {
			Node cur = pq.delMin();
			if (cur.board.isGoal())
				return cur;

			for (Board neighbor : cur.board.neighbors()) {
				if (cur.pre == null || !neighbor.equals(cur.pre.board))
					pq.insert(new Node(cur, neighbor, cur.move + 1, cur.isInitBoard));
			}
		}
		return null;
	}

	private static class Node implements Comparable<Node> {
		final Node pre;
		final Board board;
		final int move;
		final int priority;
		final boolean isInitBoard;

		Node(Node pre, Board board, int move, boolean isInitBoard) {
			this.pre = pre;
			this.board = board;
			this.move = move;
			this.isInitBoard = isInitBoard;
			// 增加manhattan比重更快找到解，但不是最短路徑
//			this.priority = (board.manhattan() * 5 + move) * 2 + board.hamming();
			this.priority = board.manhattan() + move;
		}

		@Override
		public int compareTo(Node o) {
			// 這裡用if都會超時
//			if( this.priorityM == o.priorityM )
//				return this.priorityH - o.priorityH;
//			return this.priorityM - o.priorityM;
			return this.priority - o.priority;
		}

	}

	private static int priorityH(Node n) {
		return n.board.hamming() + n.move;
	}

	private static int priorityM(Node n) {
		return n.board.manhattan() + n.move;
	}

	public boolean isSolvable() {
		return this.goal != null && this.goal.isInitBoard;
	}

	public int moves() { // min number of moves to solve initial board; -1 if unsolvable
		if (isSolvable())
			return this.goal.move;
		return -1;
	}

	public Iterable<Board> solution() { // sequence of boards in a shortest solution; null if unsolvable
		if (!isSolvable())
			return null;

		LinkedList<Board> rtn = new LinkedList<>();
		Node cur = this.goal;
		while (cur != null) {
			rtn.addFirst(cur.board);
			cur = cur.pre;
		}
		return rtn;
	}

}
```






















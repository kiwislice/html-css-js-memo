---
title: 'Week 1 編程作業:Percolation'
date: 2019-04-18
weight: 1
tags: ["coursera","Algorithms, Part I",algorithm","union-find"]
---



## 編程作業: Percolation

100分關鍵在使用2個`WeightedQuickUnionUF`，一個繪圖用，一個判斷滲透用

### Percolation.java

```java
import edu.princeton.cs.algs4.WeightedQuickUnionUF;

public class Percolation {

	private static final int FULL_ROOT = 0;
	private final int n;
	private final boolean[] open;
	private final WeightedQuickUnionUF uf;
	private final WeightedQuickUnionUF ufForCheckPercolates;
	private final int END_ROOT;
	private int openSize;
	private boolean percolates;

	public Percolation(int n) {
		if (n <= 0)
			throw new java.lang.IllegalArgumentException();

		this.n = n;
		final int size = n * n + 2;
		this.open = new boolean[size];
		this.uf = new WeightedQuickUnionUF(size);
		this.ufForCheckPercolates = new WeightedQuickUnionUF(size);
		this.END_ROOT = size - 1;
	}

	private int rcToIndex(int row, int col) {
		if (row < 1 || row > n || col < 1 || col > n)
			throw new java.lang.IllegalArgumentException();
		return (row - 1) * n + (col - 1) + 1;
	}

	public void open(int row, int col) {
		int i = rcToIndex(row, col);
		if (this.open[i])
			return;

		this.open[i] = true;
		this.openSize++;

		if (row == 1)
			union(FULL_ROOT, i);
		if (row == n)
			this.ufForCheckPercolates.union(END_ROOT, i);

		if (row > 1 && this.open[i - n])
			union(i, i - n);
		if (row < n && this.open[i + n])
			union(i, i + n);
		if (col > 1 && this.open[i - 1])
			union(i, i - 1);
		if (col < n && this.open[i + 1])
			union(i, i + 1);
	}

	private void union(int i, int j) {
		this.uf.union(i, j);
		this.ufForCheckPercolates.union(i, j);
	}

	public boolean isFull(int row, int col) {
		int i = rcToIndex(row, col);
		return this.open[i] && this.uf.connected(FULL_ROOT, i);
	}

	public boolean isOpen(int row, int col) {
		return this.open[rcToIndex(row, col)];
	}

	public int numberOfOpenSites() {
		return this.openSize;
	}

	public boolean percolates() {
		if (!this.percolates)
			this.percolates = this.ufForCheckPercolates.connected(FULL_ROOT, END_ROOT);
		return this.percolates;
	}

}
```



### PercolationStats.java

```java
import edu.princeton.cs.algs4.StdOut;
import edu.princeton.cs.algs4.StdRandom;
import edu.princeton.cs.algs4.StdStats;

public class PercolationStats {

	private static final double CONFIDENCE_95 = 1.96;
	private final double mean;
	private final double stddev;
	private final double confidenceDiff;

	public PercolationStats(int n, int trials) {
		if (n <= 0 || trials <= 0)
			throw new java.lang.IllegalArgumentException();

		double[] results = new double[trials];
		for (int i = 0; i < trials; i++) {
			results[i] = test(n);
		}

		this.mean = StdStats.mean(results);
		this.stddev = StdStats.stddev(results);
		this.confidenceDiff = CONFIDENCE_95 * this.stddev / Math.sqrt(trials);
	}

	private static double test(int n) {
		Percolation p = new Percolation(n);
		int r, c;
		while (!p.percolates()) {
			r = StdRandom.uniform(1, n + 1);
			c = StdRandom.uniform(1, n + 1);
			if (!p.isOpen(r, c))
				p.open(r, c);
		}
		return p.numberOfOpenSites() * 1.0 / (n * n);
	}

	public double mean() {
		return this.mean;
	}

	public double stddev() {
		return this.stddev;
	}

	public double confidenceLo() {
		return this.mean - this.confidenceDiff;
	}

	public double confidenceHi() {
		return this.mean + this.confidenceDiff;
	}

	public static void main(String[] args) {
		int n = Integer.parseInt(args[0]);
		int T = Integer.parseInt(args[1]);

		PercolationStats ps = new PercolationStats(n, T);
		StdOut.println("mean                    = " + ps.mean());
		StdOut.println("stddev                  = " + ps.stddev());
		StdOut.println("95% confidence interval = [" + ps.confidenceLo() + ", " + ps.confidenceHi() + "]");
	}

}

```




















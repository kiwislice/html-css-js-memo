---
title: 'Week 5 編程作業:Kd-Trees'
date: 2019-04-18
weight: 40
tags: ["coursera","Algorithms, Part I","algorithm","tree","binary search"]
---



## 編程作業: Kd-Trees

100分關鍵在如果當前最短距離>區域到點最短距離時才找，區域到點的最短距離可先用點到線的距離比過



### PointSET.java

```java
import java.util.LinkedList;
import java.util.List;
import java.util.TreeSet;

import edu.princeton.cs.algs4.Point2D;
import edu.princeton.cs.algs4.RectHV;

public class PointSET {

	private TreeSet<Point2D> set;

	public PointSET() {
		this.set = new TreeSet<>();
	}

	public boolean isEmpty() {
		return this.set.isEmpty();
	}

	public int size() {
		return this.set.size();
	}

	public void insert(Point2D p) {
		requireNonNull(p);
		this.set.add(p);
	}

	public boolean contains(Point2D p) {
		requireNonNull(p);
		return this.set.contains(p);
	}

	public void draw() {
		for (Point2D p : this.set)
			p.draw();
	}

	public Iterable<Point2D> range(RectHV rect) {
		requireNonNull(rect);
		List<Point2D> rtn = new LinkedList<>();
		for (Point2D anyP : this.set) {
			if (rect.contains(anyP)) {
				rtn.add(anyP);
			}
		}
		return rtn;
	}

	public Point2D nearest(Point2D p) {
		requireNonNull(p);

		double minDis = Double.MAX_VALUE;
		Point2D rtn = null;
		for (Point2D anyP : this.set) {
			double dis = p.distanceSquaredTo(anyP);
			if (dis < minDis) {
				minDis = dis;
				rtn = anyP;
			}
		}
		return rtn;
	}

	private static void requireNonNull(Object o) {
		if (o == null)
			throw new java.lang.IllegalArgumentException();
	}

}
```



### KdTree.java

```java
import java.util.LinkedList;
import java.util.List;
import edu.princeton.cs.algs4.Point2D;
import edu.princeton.cs.algs4.RectHV;
import edu.princeton.cs.algs4.StdDraw;

public class KdTree {

	private Node root;
	private int size;

	public KdTree() {
	}

	public boolean isEmpty() {
		return this.root == null;
	}

	public int size() {
		return this.size;
	}

	public void insert(Point2D p) {
		requireNonNull(p);
		this.root = put(root, p, true);
	}

	private Node put(Node n, Point2D p, boolean byX) {
		if (n == null) {
			this.size++;
			Node rtn = new Node(p);
//			rtn.index = this.size;
			return rtn;
		}
		if (n.p.x() == p.x() && n.p.y() == p.y())
			return n;

		boolean goLeft = (byX && p.x() < n.p.x()) || (!byX && p.y() < n.p.y());

		if (goLeft) {
			n.left = put(n.left, p, !byX);
		} else {
			n.right = put(n.right, p, !byX);
		}
		return n;
	}

	public boolean contains(Point2D p) {
		requireNonNull(p);
		return find(this.root, p, true) != null;
	}

	private Node find(Node n, Point2D p, boolean byX) {
		if (n == null)
			return null;
		if (n.p.x() == p.x() && n.p.y() == p.y())
			return n;

		boolean goLeft = (byX && p.x() < n.p.x()) || (!byX && p.y() < n.p.y());

		if (goLeft) {
			return find(n.left, p, !byX);
		} else {
			return find(n.right, p, !byX);
		}
	}

	public void draw() {
		preorderDraw(this.root, true, new RectHV(0, 0, 1, 1));
	}

	private static void preorderDraw(Node n, boolean byX, RectHV rect) {
		if (n == null)
			return;

		StdDraw.setPenColor(StdDraw.BLACK);
		StdDraw.setPenRadius(0.01);
		n.p.draw();
//		StdDraw.text(n.p.x(), n.p.y(), String.valueOf(n.index));

		if (byX) {
			StdDraw.setPenColor(StdDraw.RED);
			StdDraw.setPenRadius(0.003);
			StdDraw.line(n.p.x(), rect.ymin(), n.p.x(), rect.ymax());
			preorderDraw(n.left, !byX, new RectHV(rect.xmin(), rect.ymin(), n.p.x(), rect.ymax()));
			preorderDraw(n.right, !byX, new RectHV(n.p.x(), rect.ymin(), rect.xmax(), rect.ymax()));
		} else {
			StdDraw.setPenColor(StdDraw.BLUE);
			StdDraw.setPenRadius(0.003);
			StdDraw.line(rect.xmin(), n.p.y(), rect.xmax(), n.p.y());
			preorderDraw(n.left, !byX, new RectHV(rect.xmin(), rect.ymin(), rect.xmax(), n.p.y()));
			preorderDraw(n.right, !byX, new RectHV(rect.xmin(), n.p.y(), rect.xmax(), rect.ymax()));
		}
	}

	public Iterable<Point2D> range(RectHV rect) {
		requireNonNull(rect);
		List<Point2D> rtn = new LinkedList<>();
		find(this.root, rect, true, rtn);
		return rtn;
	}

	private static void find(Node n, RectHV rect, boolean byX, List<Point2D> rtn) {
		if (n == null)
			return;
		if (rect.contains(n.p)) {
			rtn.add(n.p);
			find(n.left, rect, !byX, rtn);
			find(n.right, rect, !byX, rtn);
			return;
		}

		if (byX) {
			if (rect.xmin() <= n.p.x() && n.p.x() <= rect.xmax()) {
				find(n.left, rect, !byX, rtn);
				find(n.right, rect, !byX, rtn);
			} else if (rect.xmax() < n.p.x()) {
				find(n.left, rect, !byX, rtn);
			} else {
				find(n.right, rect, !byX, rtn);
			}
		} else {
			if (rect.ymin() <= n.p.y() && n.p.y() <= rect.ymax()) {
				find(n.left, rect, !byX, rtn);
				find(n.right, rect, !byX, rtn);
			} else if (rect.ymax() < n.p.y()) {
				find(n.left, rect, !byX, rtn);
			} else {
				find(n.right, rect, !byX, rtn);
			}
		}
	}

	public Point2D nearest(Point2D p) {
		requireNonNull(p);
		FindNearestData data = new FindNearestData();
		findNearest(this.root, p, true, data, new RectHV(0, 0, 1, 1));
		Point2D nearest = data.nearest == null ? null : data.nearest.p;
		return nearest;
	}

	private void findNearest(Node n, Point2D p, boolean byX, FindNearestData data, RectHV findIn) {
//		System.out.println("findNearest " + n);
		if (n == null)
			return;

		double disToLine = byX ? n.p.x() - p.x() : n.p.y() - p.y();
		disToLine = Math.abs(disToLine);

		if (data.minDis >= disToLine) {
			double disToP = n.p.distanceTo(p);
			if (disToP < data.minDis) {
				data.minDis = disToP;
				data.nearest = n;
			}
			if (data.minDis == 0)
				return;
		}

		boolean goLeftFirst = (byX && p.x() < n.p.x()) || (!byX && p.y() < n.p.y());
		if (goLeftFirst) {
			if (n.left != null) {
				findNearest(n.left, p, !byX, data, nextRect(findIn, n, byX, true));
			}
			if (n.right != null && data.minDis > disToLine) {
				RectHV nextRect = nextRect(findIn, n, byX, false);
				if (data.minDis > nextRect.distanceTo(p))
					findNearest(n.right, p, !byX, data, nextRect);
			}
		} else {
			if (n.right != null) {
				findNearest(n.right, p, !byX, data, nextRect(findIn, n, byX, false));
			}
			if (n.left != null && data.minDis > disToLine) {
				RectHV nextRect = nextRect(findIn, n, byX, true);
				if (data.minDis > nextRect.distanceTo(p))
					findNearest(n.left, p, !byX, data, nextRect);
			}
		}
	}

	private static RectHV nextRect(RectHV curFindIn, Node curNode, boolean curByX, boolean goLeft) {
		if (curByX) {
			if (goLeft)
				return new RectHV(curFindIn.xmin(), curFindIn.ymin(), curNode.p.x(), curFindIn.ymax());
			else
				return new RectHV(curNode.p.x(), curFindIn.ymin(), curFindIn.xmax(), curFindIn.ymax());
		} else {
			if (goLeft)
				return new RectHV(curFindIn.xmin(), curFindIn.ymin(), curFindIn.xmax(), curNode.p.y());
			else
				return new RectHV(curFindIn.xmin(), curNode.p.y(), curFindIn.xmax(), curFindIn.ymax());
		}
	}

	private static class FindNearestData {
		Node nearest = null;
		double minDis = Double.MAX_VALUE;
	}

	private static void requireNonNull(Object o) {
		if (o == null)
			throw new java.lang.IllegalArgumentException();
	}

	private static class Node {
		Node left;
		Node right;
		Point2D p;
//		int index = -1;

		Node(Point2D p) {
			this.p = p;
		}

		@Override
		public String toString() {
			return String.valueOf(p);
		}

	}

}
```






















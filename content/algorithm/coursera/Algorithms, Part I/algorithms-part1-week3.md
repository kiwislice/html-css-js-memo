---
title: 'Week 3 編程作業:Collinear Points'
date: 2019-04-18
weight: 20
tags: ["coursera","Algorithms, Part I","algorithm","sort"]
---



## 編程作業: Collinear Points

100分關鍵是FastCollinearPoints在判斷線段重複時不使用Set或Sort，而是只有當前點是當前線段的左下端點時才加入



### Point.java

```java

/******************************************************************************
 *  Compilation:  javac Point.java
 *  Execution:    java Point
 *  Dependencies: none
 *  
 *  An immutable data type for points in the plane.
 *  For use on Coursera, Algorithms Part I programming assignment.
 *
 ******************************************************************************/

import java.util.Comparator;
import edu.princeton.cs.algs4.StdDraw;

public class Point implements Comparable<Point> {

	private final int x; // x-coordinate of this point
	private final int y; // y-coordinate of this point

	/**
	 * Initializes a new point.
	 *
	 * @param x the <em>x</em>-coordinate of the point
	 * @param y the <em>y</em>-coordinate of the point
	 */
	public Point(int x, int y) {
		/* DO NOT MODIFY */
		this.x = x;
		this.y = y;
	}

	/**
	 * Draws this point to standard draw.
	 */
	public void draw() {
		/* DO NOT MODIFY */
		StdDraw.point(x, y);
	}

	/**
	 * Draws the line segment between this point and the specified point to standard
	 * draw.
	 *
	 * @param that the other point
	 */
	public void drawTo(Point that) {
		/* DO NOT MODIFY */
		StdDraw.line(this.x, this.y, that.x, that.y);
	}

	/**
	 * Returns the slope between this point and the specified point. Formally, if
	 * the two points are (x0, y0) and (x1, y1), then the slope is (y1 - y0) / (x1 -
	 * x0). For completeness, the slope is defined to be +0.0 if the line segment
	 * connecting the two points is horizontal; Double.POSITIVE_INFINITY if the line
	 * segment is vertical; and Double.NEGATIVE_INFINITY if (x0, y0) and (x1, y1)
	 * are equal.
	 *
	 * @param that the other point
	 * @return the slope between this point and the specified point
	 */
	public double slopeTo(Point that) {
		/* YOUR CODE HERE */
		if (that == null)
			throw new java.lang.NullPointerException();

		if (that.x == this.x)
			return that.y == this.y ? Double.NEGATIVE_INFINITY : Double.POSITIVE_INFINITY;
		return that.y == this.y ? 0 : (that.y - this.y) * 1.0 / (that.x - this.x);
	}

	/**
	 * Compares two points by y-coordinate, breaking ties by x-coordinate. Formally,
	 * the invoking point (x0, y0) is less than the argument point (x1, y1) if and
	 * only if either y0 < y1 or if y0 = y1 and x0 < x1.
	 *
	 * @param that the other point
	 * @return the value <tt>0</tt> if this point is equal to the argument point (x0
	 *         = x1 and y0 = y1); a negative integer if this point is less than the
	 *         argument point; and a positive integer if this point is greater than
	 *         the argument point
	 */
	public int compareTo(Point that) {
		/* YOUR CODE HERE */
		if (that == null)
			throw new java.lang.NullPointerException();

		int i = this.y - that.y;
		return i == 0 ? this.x - that.x : i;
	}

	/**
	 * Compares two points by the slope they make with this point. The slope is
	 * defined as in the slopeTo() method.
	 *
	 * @return the Comparator that defines this ordering on points
	 */
	public Comparator<Point> slopeOrder() {
		/* YOUR CODE HERE */
		return new Comparator<Point>() {
			@Override
			public int compare(Point p1, Point p2) {
				if (p1 == null || p2 == null)
					throw new java.lang.NullPointerException();
				return Double.compare(slopeTo(p1), slopeTo(p2));
			}
		};
	}

	/**
	 * Returns a string representation of this point. This method is provide for
	 * debugging; your program should not rely on the format of the string
	 * representation.
	 *
	 * @return a string representation of this point
	 */
	public String toString() {
		/* DO NOT MODIFY */
		return "(" + x + ", " + y + ")";
	}

	/**
	 * Unit tests the Point data type.
	 */
	public static void main(String[] args) {

	}
}
```



### BruteCollinearPoints.java

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class BruteCollinearPoints {

	private final List<LineSegment> list;

	public BruteCollinearPoints(Point[] points) {
		if (points == null)
			throw new java.lang.IllegalArgumentException();

		checkPoints(points);
		this.list = new ArrayList<>();
		if (points.length >= 4)
			brute(points);
	}

	private void brute(Point[] points) {
		for (int a = 0; a < points.length - 3; a++) {
			for (int b = a + 1; b < points.length - 2; b++) {
				for (int c = b + 1; c < points.length - 1; c++) {
					for (int d = c + 1; d < points.length; d++) {
						if (collinear(points[a], points[b], points[c], points[d]))
							addLineSegment(points[a], points[b], points[c], points[d]);
					}
				}
			}
		}
	}

	private static boolean collinear(Point a, Point b, Point c, Point d) {
		final double slopeAB = a.slopeTo(b);
		return slopeAB == a.slopeTo(c) && slopeAB == a.slopeTo(d);
	}

	private void addLineSegment(Point... ps) {
		Arrays.sort(ps);
		this.list.add(new LineSegment(ps[0], ps[ps.length - 1]));
	}

	private static void checkPoints(Point... ps) {
		for (Point p : ps) {
			if (p == null)
				throw new java.lang.IllegalArgumentException();
		}
		for (int i = 0; i < ps.length - 1; i++) {
			for (int j = i + 1; j < ps.length; j++) {
				if (ps[i].compareTo(ps[j]) == 0)
					throw new java.lang.IllegalArgumentException();
			}
		}
	}

	public int numberOfSegments() {
		return this.list.size();
	}

	public LineSegment[] segments() {
		return this.list.toArray(new LineSegment[0]);
	}

}
```



### FastCollinearPoints.java

```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;

public class FastCollinearPoints {

	private final List<LineSegment> list;
	private final ComputePoint[] cps;

	public FastCollinearPoints(Point[] points) {
		checkNullPoints(points);

		Point[] clone = new Point[points.length];
		System.arraycopy(points, 0, clone, 0, points.length);
		checkRepeatPoints(clone);

		this.list = new ArrayList<>();

		this.cps = new ComputePoint[points.length];
		for (int i = 0; i < points.length; i++)
			this.cps[i] = new ComputePoint();

		for (int i = 0; i < points.length - 3; i++)
			fast(clone, i);
	}

	private static void checkNullPoints(Point[] ps) {
		if (ps == null)
			throw new java.lang.IllegalArgumentException();

		for (Point p : ps) {
			if (p == null)
				throw new java.lang.IllegalArgumentException();
		}
	}

	private static void checkRepeatPoints(Point[] ps) {
		Arrays.sort(ps);
		for (int i = 1; i < ps.length; i++) {
			if (ps[i - 1].compareTo(ps[i]) == 0)
				throw new java.lang.IllegalArgumentException();
		}
	}

	private void fast(Point[] ps, int cur) {
		Point curP = ps[cur];

		for (int i = 0; i < ps.length; i++) {
			this.cps[i].p = ps[i];
			this.cps[i].slope = curP.slopeTo(ps[i]);
		}

		Arrays.sort(cps, ComputePoint.SORT_BY_SLOPE);

		int c = 1;
		for (int i = 1; i < ps.length; i++) {
			if (cps[i - 1].slope == cps[i].slope) {
				c++;
				continue;
			}
			if (c >= 3) {
				addLine(curP, i - c, c);
			}
			c = 1;
		}
		if (c >= 3) {
			int i = ps.length;
			addLine(curP, i - c, c);
		}
	}

	private void addLine(Point curP, int cpsS, int len) {
		Point min = min(curP, cps[cpsS].p);
		Point max = max(curP, cps[cpsS + len - 1].p);

		if (curP != min)
			return;

		this.list.add(new LineSegment(min, max));
	}

	private static Point min(Point a, Point b) {
		return a.compareTo(b) > 0 ? b : a;
	}

	private static Point max(Point a, Point b) {
		return a.compareTo(b) < 0 ? b : a;
	}

	private static class ComputePoint {
		Point p;
		double slope;

		static final Comparator<ComputePoint> SORT_BY_SLOPE = new Comparator<FastCollinearPoints.ComputePoint>() {
			@Override
			public int compare(ComputePoint o1, ComputePoint o2) {
				return Double.compare(o1.slope, o2.slope);
			}
		};
		static final Comparator<ComputePoint> SORT_BY_P = new Comparator<FastCollinearPoints.ComputePoint>() {
			@Override
			public int compare(ComputePoint o1, ComputePoint o2) {
				return o1.p.compareTo(o2.p);
			}
		};
	}

	public int numberOfSegments() {
		return this.list.size();
	}

	public LineSegment[] segments() {
		return this.list.toArray(new LineSegment[0]);
	}

}
```
















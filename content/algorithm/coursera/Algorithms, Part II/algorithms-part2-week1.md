---
title: 'Week 1 編程作業:WordNet'
date: 2019-05-15
weight: 1
tags: ["coursera","Algorithms, Part II","algorithm","graph","directed graph"]
---



## 編程作業: WordNet

100分

- 檢查沒有環且同根
- 如果自製BFS可以更快(搜尋到共同祖先即停止)

### WordNet.java

```java
import java.util.HashMap;
import java.util.HashSet;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

import edu.princeton.cs.algs4.Digraph;
import edu.princeton.cs.algs4.DirectedCycle;
import edu.princeton.cs.algs4.In;

public class WordNet {

	private final Map<Integer, String> synsetsById;
	private final Map<String, Set<Integer>> synsets;
	private final Digraph digraph;
	private final SAP sap;
	private final Iterable<String> iterable = () -> new NounsIterator();

	// constructor takes the name of the two input files
	public WordNet(String synsets, String hypernyms) {
		this(new In(requireNonNull(synsets)), new In(requireNonNull(hypernyms)));
	}

	private WordNet(In synsets, In hypernyms) {
		requireNonNull(synsets);
		requireNonNull(hypernyms);

		this.synsetsById = new HashMap<>();
		this.synsets = new HashMap<>();
		int maxId = readSynsets(synsets);
		this.digraph = new Digraph(maxId + 1);
		initDigraphByHypernyms(this.digraph, hypernyms);
		this.sap = new SAP(this.digraph);

		requireNoCycle(this.digraph);
		requireOneRoot(this.digraph);
	}

	private int readSynsets(In in) {
		int maxId = 0;
		while (in.hasNextLine()) {
			String[] ar = in.readLine().split(",");
			int id = Integer.parseInt(ar[0]);
			this.synsetsById.put(id, ar[1]);
			String[] sar = ar[1].split("\\s+");
			for (String s : sar) {
				Set<Integer> idset = this.synsets.get(s);
				if (idset == null) {
					idset = new HashSet<>();
					this.synsets.put(s, idset);
				}
				idset.add(id);
			}
			maxId = maxId < id ? id : maxId;
		}
		return maxId;
	}

	private static void initDigraphByHypernyms(Digraph digraph, In in) {
		while (in.hasNextLine()) {
			String[] ar = in.readLine().split(",");
			int id = Integer.parseInt(ar[0]);
			for (int c = 1; c < ar.length; c++) {
				int up = Integer.parseInt(ar[c]);
				digraph.addEdge(id, up);
			}
		}
	}

	// returns all WordNet nouns
	public Iterable<String> nouns() {
		return iterable;
	}

	// is the word a WordNet noun?
	public boolean isNoun(String word) {
		requireNonNull(word);
		return this.synsets.containsKey(word);
	}

	// distance between nounA and nounB (defined below)
	public int distance(String nounA, String nounB) {
		requireNoun(nounA);
		requireNoun(nounB);

		Set<Integer> idA = this.synsets.get(nounA);
		Set<Integer> idB = this.synsets.get(nounB);

		return this.sap.length(idA, idB);
	}

	// a synset (second field of synsets.txt) that is the common ancestor of nounA
	// and nounB
	// in a shortest ancestral path (defined below)
	public String sap(String nounA, String nounB) {
		requireNoun(nounA);
		requireNoun(nounB);

		Set<Integer> idA = this.synsets.get(nounA);
		Set<Integer> idB = this.synsets.get(nounB);

		int ancestor = this.sap.ancestor(idA, idB);
		return this.synsetsById.get(ancestor);
	}

	private static <T> T requireNonNull(T obj) {
		if (obj == null)
			throw new IllegalArgumentException();
		return obj;
	}

	private String requireNoun(String s) {
		if (!isNoun(s))
			throw new IllegalArgumentException();
		return s;
	}

	private static Digraph requireNoCycle(Digraph G) {
		DirectedCycle dc = new DirectedCycle(G);
		if (dc.hasCycle())
			throw new IllegalArgumentException();
		return G;
	}

	private static void requireOneRoot(Digraph G) {
		int count = 0;
		for (int i = 0; i < G.V(); i++) {
			if (G.outdegree(i) == 0)
				count++;
		}
		if (count != 1)
			throw new IllegalArgumentException();
	}

	private class NounsIterator implements Iterator<String> {

		private final Iterator<String> it = synsets.keySet().iterator();

		@Override
		public boolean hasNext() {
			return it.hasNext();
		}

		@Override
		public String next() {
			return it.next();
		}

	}

}
```



### SAP.java

```java
import java.util.HashMap;
import java.util.Map;
import edu.princeton.cs.algs4.BreadthFirstDirectedPaths;
import edu.princeton.cs.algs4.Digraph;

public class SAP {

	private final Digraph G;
	private final Map<IdPair, Result> cache;

	// constructor takes a digraph (not necessarily a DAG)
	public SAP(Digraph G) {
		requireNonNull(G);
		this.G = G;
		this.cache = new HashMap<>();
	}

	// length of shortest ancestral path between v and w; -1 if no such path
	public int length(int v, int w) {
		validateVertex(v);
		validateVertex(w);

		IdPair idPair = new IdPair(v, w);
		Result result = cache.get(idPair);
		if (result == null) {
			result = sap(v, w);
			cache.put(idPair, result);
		}
		return result.length;
	}

	// a common ancestor of v and w that participates in a shortest ancestral path;
	// -1 if no such path
	public int ancestor(int v, int w) {
		validateVertex(v);
		validateVertex(w);

		IdPair idPair = new IdPair(v, w);
		Result result = cache.get(idPair);
		if (result == null) {
			result = sap(v, w);
			cache.put(idPair, result);
		}
		return result.ancestor;
	}

	// length of shortest ancestral path between any vertex in v and any vertex in
	// w; -1 if no such path
	public int length(Iterable<Integer> v, Iterable<Integer> w) {
		requireNonNull(v);
		requireNonNull(w);

		BreadthFirstDirectedPaths bfdpV = new BreadthFirstDirectedPaths(this.G, v);
		BreadthFirstDirectedPaths bfdpW = new BreadthFirstDirectedPaths(this.G, w);
		return sap(bfdpV, bfdpW).length;
	}

	// a common ancestor that participates in shortest ancestral path; -1 if no such
	// path
	public int ancestor(Iterable<Integer> v, Iterable<Integer> w) {
		requireNonNull(v);
		requireNonNull(w);

		BreadthFirstDirectedPaths bfdpV = new BreadthFirstDirectedPaths(this.G, v);
		BreadthFirstDirectedPaths bfdpW = new BreadthFirstDirectedPaths(this.G, w);
		return sap(bfdpV, bfdpW).ancestor;
	}

	private Result sap(int idA, int idB) {
		if (idA == idB) {
			return new Result(0, idA);
		}

		BreadthFirstDirectedPaths bfdpV = new BreadthFirstDirectedPaths(this.G, idA);
		BreadthFirstDirectedPaths bfdpW = new BreadthFirstDirectedPaths(this.G, idB);
		return sap(bfdpV, bfdpW);
	}

	private Result sap(BreadthFirstDirectedPaths v, BreadthFirstDirectedPaths w) {
		int length = -1;
		int ancestor = -1;
		for (int i = 0; i < this.G.V(); i++) {
			if (v.hasPathTo(i) && w.hasPathTo(i)) {
				if (length < 0 || length > v.distTo(i) + w.distTo(i)) {
					length = v.distTo(i) + w.distTo(i);
					ancestor = i;
				}
			}
		}
		return new Result(length, ancestor);
	}

	private static <T> T requireNonNull(T obj) {
		if (obj == null)
			throw new IllegalArgumentException();
		return obj;
	}

	private static <T> Iterable<T> requireNonNull(Iterable<T> obj) {
		if (obj == null)
			throw new IllegalArgumentException();
		for (T o : obj)
			if (o == null)
				throw new IllegalArgumentException();
		return obj;
	}

	private void validateVertex(int v) {
		int V = this.G.V();
		if (v < 0 || v >= V)
			throw new IllegalArgumentException();
	}

	private static class IdPair {
		private final int idMin;
		private final int idMax;

		IdPair(int idA, int idB) {
			if (idA <= idB) {
				this.idMin = idA;
				this.idMax = idB;
			} else {
				this.idMin = idB;
				this.idMax = idA;
			}
		}

		@Override
		public int hashCode() {
			final int prime = 31;
			int result = 1;
			result = prime * result + idMax;
			result = prime * result + idMin;
			return result;
		}

		@Override
		public boolean equals(Object obj) {
			if (this == obj)
				return true;
			if (obj == null)
				return false;
			if (getClass() != obj.getClass())
				return false;
			IdPair other = (IdPair) obj;
			if (idMax != other.idMax)
				return false;
			if (idMin != other.idMin)
				return false;
			return true;
		}

	}

	private static class Result {
		private final int length;
		private final int ancestor;

		Result(int length, int ancestor) {
			this.length = length;
			this.ancestor = ancestor;
		}
	}

}

```



### Outcast.java

```java

public class Outcast {

	private final WordNet wordnet;

	public Outcast(WordNet wordnet) {
		this.wordnet = wordnet;
	}

	public String outcast(String[] nouns) {
		long[] ds = new long[nouns.length];
		int maxIndex = 0;
		for (int i = 0; i < nouns.length; i++) {
			for (int j = i + 1; j < nouns.length; j++) {
				int d = this.wordnet.distance(nouns[i], nouns[j]);
				ds[i] += d;
				ds[j] += d;
				if (ds[maxIndex] < ds[i])
					maxIndex = i;
				if (ds[maxIndex] < ds[j])
					maxIndex = j;
			}
		}
		return nouns[maxIndex];
	}

}

```




















#### Remove Cycle Edges  - Description

* created by: Jiankai Sun
* last update: 21 March, 2017
* Email: sun.1306@osu.edu

##### Requirements

* Python 2.7
* Lib: networkx
* Lib: TrueSkill [install](http://trueskill.org/)

#### Generate Random Graphs (DAGs)

```
python generate_random_dag.py -n 300 -m 2500 -g data/gnm_300_2500.edges
```

* n: number of nodes
* m: number of edges 
* g: file path used to save the generated random graph

#### Introduce Cycles to DAG

```
python introduce_cycles_to_DAG.py -g data/gnm_300_2500.edges -k 300 -l 0
```
* -g:  target DAG edges list file 
* -k: number of extra edges introduced to make DAG have cycles 
* -l: threshold to control path length, if l <=0, no constraints on path length, else: path length < l (cycle length <= l)

output:
 
*  extra_edges_file (**Ground Truth Edges**): gnm_300_2500_extra_300_path_len_0.edges
*  graph_with_extra_edges_file : gnm_300_2500_graph_w_extra_300_path_len_0.edges

#### Breaking Cycles by DFS

```
python remove_cycle_edges_by_dfs.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges 
```

You can specificity a edges list of file as ground truth (edges should be removed to break cycles)  by using '-t'. It will report precision, recall and F-1 score.

```
python remove_cycle_edges_by_dfs.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -t data/gnm_300_2500_extra_300_path_len_0.edges
``` 

Performance will be:


#### Breaking Cycles by MFAS

Local greedy implementation of Minimum feedback arc set problem.

```
python remove_cycle_edges_by_minimum_feedback_arc_set_greedy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges
```

You can specificity a edges list of file as ground truth (edges should be removed to break cycles)  by using '-t'. It will report precision, recall and F-1 score.

```
python remove_cycle_edges_by_minimum_feedback_arc_set_greedy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -t data/gnm_300_2500_extra_300_path_len_0.edges
```

#### Breaking Cycles via Hierarchy, inferred by PageRank

Graph hierarchy is inferred from PageRank.

```
python remove_cycle_edges_by_hierarchy.py -s pagerank -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges
```

* -s pagerank: use pagerank to infer graph hierarchy

You can also specify the ground truth file path as ground_truth_edges_file by using '-t'

```
python remove_cycle_edges_by_hierarchy.py -s pagerank -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -t data/gnm_300_2500_extra_300_path_len_0.edges
```


#### Breaking Cycles via Hierarchy, inferred by TrueSkill

* Requirement: TrueSkill [install](http://trueskill.org/)

```
python remove_cycle_edges_by_hierarchy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -s trueskill
```

* -s trueskill: use TrueSkill to infer graph hierarchy

You can also specify the ground truth file path as ground_truth_edges_file by using '-t'.

```
python remove_cycle_edges_by_hierarchy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -s trueskill -t data/gnm_300_2500_extra_300_path_len_0.edges
```

It will report performance of TS_G, TS_B, TS_F, and TS_Voting (ensembling of TS_G, TS_B and TS_F).



#### Breaking Cycles via Hierarchy, inferred by SocialAgony

Social Agony code is from [Tatti](http://users.ics.aalto.fi/ntatti/software.shtml)

The source code has been put in /agony. You have to compile it first. To compile it sucessfully, you may have to install some packages. Detailes can be viewed at /agony/README.md

After that run:

```
python remove_cycle_edges_by_hierarchy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -s socialagony
```

* -s socialagony: use SocialAgony to infer graph hierarchy

You can also specify the ground truth file path as ground_truth_edges_file by using '-t'.

```
python remove_cycle_edges_by_hierarchy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -s socialagony -t data/gnm_300_2500_extra_300_path_len_0.edges
```

It will report performance of SA_G, SA_B, SA_F, and SA_Voting (ensembling of SA_G, SA_B and SA_F).


#### Breaking Cycles via Hierarchy, Ensembling

Ensembling TS_G, TS_B, TS_F, SA_G, SA_B and SA_F.

```
python remove_cycle_edges_by_hierarchy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -s ensembling
```

* -s ensembling: ensembling all 6 apprpaches

You can also specify the ground truth file path as ground_truth_edges_file by using '-t'. 


```
python remove_cycle_edges_by_hierarchy.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -s ensembling -t data/gnm_300_2500_extra_300_path_len_0.edges
```

* **This can also report performances of TS_G, TS_B, TS_F, SA_G, SA_B and SA_F individually**, these performance may have slightly difference with running them individually. 
* It can report performance of H_Voting (ensembling of SA_G, SA_B, SA_F, TS_G, TS_B and TS_F)

#### Test on Synthetic Graphs

Instead of testing above methods individually, you can simply run below code to compare performance of all methods on Synthetic Graphs (random generated graphs).

```
python synthetic_performance.py --dir data/ -n 300 -m 2500 -k 300 -l 0
```

* --dir: directory to save all results, 'data/' is in current directory
* -n: number of nodes in the generated random graph
* -m: number of edges in the generated random graph 
* -k: number of extra edges to introduce cycles to the generated random graph 
* -l: control parameter of path length

It will do: 

* generate a random DAG with n nodes and m edges
* introduce k extra edges to this DAG to make it have cycles (l is used to control generated cycle size, if l == 0, it has no constraints on cycle size)
* run all methods to break cycles and report performance


#### Test on Real Datasets

If you already have a graph with cycles, you can run:

```
python break_cycles.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges
```

* -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges : to specify edgeslist file

And if you have ground truth, you can run below commands to report performances:


```
python break_cycles.py -g data/gnm_300_2500_graph_w_extra_300_path_len_0.edges -t data/gnm_300_2500_extra_300_path_len_0.edges 
```

* -t data/gnm_300_2500_extra_300_path_len_0.edges : to specify ground truth of edges to be removed


#### Visualization 

Visualize peroformance of different methods.

```
python plot_recallPrecision.py --input data/performance.txt --title "RG(1.5K,15K)"
```

* --input data/performance.txt 

file format:

method_name  precision recall f-1Score(optional) numEdgesRemoved(optional) 

**(columns are separated by whitespace)**

First **3** columns must be: method_name, precision, and recall. 

Other columns are optional, such as f-1score and numEdgesRemoved in data/performance.txt.


* --title "RG(1.5K,15K)": file will be saved at: performance_RG(1.5K,15K).pdf

For example, we test algorithms on a random graph (1500,15000) with 500 extra edges added, and get performance saved as:


```
DFS 	0.0140 	0.1820 	0.0259 	6516
PR 	0.2298 	0.7400 	0.3507 	1610
MFAS 	0.4464 	0.9320 	0.6036 	1044
SA_G 	0.8993 	0.9640 	0.9305 	536
SA_F 	0.5946 	0.9620 	0.7349 	809
SA_B 	0.5975 	0.9680 	0.7389 	810
SA_Voting 	0.9282 	0.9560 	0.9419 	515
TS_G 	0.8274 	0.9300 	0.8757 	562
TS_F 	0.5145 	0.9580 	0.6695 	931
TS_B 	0.5010 	0.9700 	0.6608 	968
TS_Voting 	0.8654 	0.9260 	0.8947 	535
H_Voting 	0.9369 	0.9500 	0.9434 	507
```

* first column, such as DFS: method name
* second column, such as 0.0140: precision
* third column, such as 0.1820: recall
* forth column, such as 0.0259: f-1 score (optional, will compute f-1 automatically)
* fifth column, such as 1044: number of edges to be removed

visualization will be saved as: performance_RG(1.5K,15K).pdf

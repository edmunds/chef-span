chef-span
===========

Description
===========
chef-span.rb is a script for querying connectivity information out of chef server instances.  The output is a collection of vertices and edges which can be used with graphing software to visual
ize your network.

Usage
===========

Usage: chef-span.rb [options] -a <chef_server_url1,client1,keyfile1,[...]>
    -h, --help                       This help print out.
    -m, --max_environments COUNT     Max number of environments to process
    -a chef_server_url,client,keyfile
        --api                        Required components for querying the chef server API
    -o, --output FILE                Output file for graph content.
    -i, --input FILE                 Input file for manual nodes.

The API flag (-a) is the only required parameter, and expects one or more comma-separated triples of a chef server url, the name of the client, and the corresponding key file for said client.

Example:  
          chef-span.rb -a https://chef.server.url:port,chef_client_name,/home/user/.chef/key.pem

To query multiple chef servers:
          chef-span.rb -a https://chef.server.url:port,chef_client_name,/home/user/.chef/key.pem,https://chef.server.url2:port,chef_client_name2,/home/user/.chef/key.pem2

At the time of this writing, chef-span will attempt to query nfs filesystems and "connections" out of the node data for
 every node in the chef server.  NFS filesystems are available by default with ohai, but "connections" are not.  "Connection"
data is gathered via an ohai plugin, and is a rolling summary of recent TCP network connections.  The connection data GREATLY
increases the value of the graph that is output by chef-span.  See https://github.com/edmunds/cookbook-connections for additional details.


If there are manual nodes and edges you want to include in the graph, you can optionally create an input file.  I advise against this ; manual entries are future prob
lems.  For those who proceed nonetheless, the syntax of the input file can be found in SAMPLE_INPUT.txt.

Visualization
===========

The output of the chef-span.rb script is a collection of vertices and edges which can be loaded into various visualization programs.  The output is in GDF format.
Below, I've outlined the use of one such tool, Gephi ( http://gephi.org ).

Visualizing with Gephi
===========

Getting Started
	Download the free, open-source Gephi client software ( available for Windows, Mac, and Linux ) from here:  http://gephi.org/  .  At the time of this writing the version is 0.8.2-beta.
	Start the Gephi application
	Close the "Welcome" pop-up window (if it appears)
	Open the file containing the network graph (the output of chef-span.rb)
		This is a two-stage process.  The first file we open is a graph definition, which contains visualization settings and presets, but no data.  The second file is the actual data, which is appended on to the first.
		First, select File -> Open, and select "gephi_settings.gephi" (included in this repo).  The background should turn black after this is loaded.
		Second, select File -> Open and select "the file outputed by chef-span.rb.
			When the pop-up screen appears for "Import Report", change the round button selection from "New Graph"  to "Append Graph" and select "Ok".  
			You should see a cube of dots appear.  The cube of dots is the un-ordered collection of nodes and edges.
	(Optional) Normalize edge weights.  Though they're not immediately visible, some connections are drawn with very thick arrows.  You can negate this by doing the following:
		Select the "Data Laboratory" tab
		Select the "Edges" button (it's in the upper left quadrant of the screen)
		Select the "Fill column with a value" button (it's at the bottom-middle)
			Select "Weight", input a "1", and select Ok
			Return to the graph by selecting the "Overview" tab in the upper-left
	From here, skip to the use case you want to explore.  These will provide filtering examples to narrow down the space you're investigating.  Then, when you're navigating through the graph, refer to the "Navigating in Gephi" section for basic operations.
	If you want to save your graph, save it locally.  The gephi_settings.gephi file is read-only by design, and the logical-network-connections.gdf is overwritten with new data every hour.

Navigating in Gephi
	Zoom with mouse wheel
	Right-click-and-drag to move your current position in the graph
	To get information about a node (like name, environment, OS, data center, run_list, hardware type, etc ) select the icon of an-arrow-with-a-question-mark-next-to-it, then select the node
	To paint the graph based on a piece of information in the nodes (for example, environment, or data center), select Partition, click the Refresh button, then use the drop down to select the attribute you want to use to separate the nodes.  Finally, click Apply.  You should see the nodes change color.
	To organize the graph based on the connections between the nodes, select Layout.  
		Select an algorithm.
			"ForceAtlas 2" is ideal for large graphs because it is multi-threaded.  
			"ForceAtlas" is good for small graphs ( less than a couple hundred nodes )
			There are several tunables you can use for each Layout algorithm.  Play with these as you like.
		Select Run.  You should see the graph go in to motion and eventually come to a halt.  On large graphs this can take a while.
		Select Stop when you're satisfied with the image of the graph.  If you let the algorithm run it will chew up CPU cycles.
	To have the node and edge names show all of the time, select the small "Up arrow" (looks like a triangle) in the bottom-right of the app, then select Labels, and uncheck the box named "Hide non-selected"
	For Filters, see the sample use cases below.

Sample Use Cases

The following are several examples that illustrate use cases for exploring the graph.  They explain how to use the Filtering functions in Gephi.

See what a single system connects to
	Select Filters on the right of the screen and expand the Topology folder
	Drag the "Ego Network" selection down to the Queries field
	Type in the name of the node you want to investigate (FQDN), click Ok, and then click Filter.  You should see a vastly reduced graph.
	Apply a layout to format the graph nicely.  ForceAtlas is probably a good choice.
	Hovering over the nodes should highlight them with their name and the edge label, which should indicate a port number or perhaps nfs filesystem path
	When you're done, return to the Filter tab, right-click on the "Ego Network" entry under Queries, and select Remove.  The rest of the graph should spring back into existence.

See how an environment interacts with itself
	Select Filters on the right of the screen and expand the Attributes folder, then Equal
	Drag the "environment String (Node)" selection down to the Queries field
	Type in the name of the environment you want to investigate , click Ok, and then click Filter.  You should see a vastly reduced graph.
	Apply a layout to format the graph nicely.  ForceAtlas is probably a good choice.
	Hovering over the nodes should highlight them with their name and the edge label, which should indicate a port number or perhaps nfs filesystem path
	When you're done, return to the Filter tab, right-click on the "Equal (environment)" entry under Queries, and select Remove.  The rest of the graph should spring back into existence.

See how an environment interacts with itself and its"neighbors"
	Select Filters on the right of the screen and expand the Topology folder
	Drag the "Neighbors Network" selection down to the Queries field
	Under Queries, expand the "Neighbors Network" selection	
	Under Filters, expand Attributes, and then expand Partition
	Drag the "environment (Node)" selection down to the "Drag subfilter here" spot under Queries
	Click on Partition
	A selection of of environments is now available below the Queries section.  Select the environment you want to investigate and click Filter.  
	Apply a layout to format the graph nicely.  ForceAtlas is probably a good choice.
	Hovering over the nodes should highlight them with their name and the edge label, which should indicate a port number or perhaps nfs filesystem path
	When you're done, return to the Filter tab, right-click on the "Neighbors Network" entry under Queries, and select Remove.  The rest of the graph should spring back into existence.

Rank systems in the enterprise based on incoming connections
	Select Ranking in the left panel, Nodes, and then using the drop down menu select In-Degree
	Click "Spline", select the second template, and click Close
	Click "Apply"
	In the lower-left corner of the Ranking frame, there is an image of a sheet of lined paper, which hover text of "Result list".  Select this icon, then click Apply.
	You should see an ordered list of nodes that have the most incoming connections.  These systems are probably important.
	Select the "ruby" icon in the upper-right of the Ranking frame, then click Apply.  This will resize nodes based on their incoming connection for ease in visualizing.

Changes
=======

## v0.0.1

- first cut

License and Author
==================

Author:: Joshua Miller (<jmiller@edmunds.com>)

Copyright:: 2013, Edmunds.com

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

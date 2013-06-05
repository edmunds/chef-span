chef-span

=========

Description
===========
The output of the chef-span.rb script is a collection of vertices and edges which can be used with graphing software to visual
ize your network.

Usage
===========



At the time of this writing, chef-span will attempt to query nfs filesystems and "connections" out of the node data for
 every node in the chef server.  NFS filesystems are available by default with ohai, but "connections" are not.  "Connection"
data is gathered via an ohai plugin, and is a rolling summary of recent TCP network connections.  The connection data GREATLY
increases the value of the graph that is output by chef-span.  See https://github.com/edmunds/cookbook-connections for additio
nal details.

        To query a single chef server:
        -a "https://chef.server.url:port,chef_client_name,/home/user/.chef/key.pem"
        To query multiple chef servers:
        -a "https://chef.server.url:port,chef_client_name,/home/user/.chef/key.pem,\
                https://chef.server.url2:port,chef_client_name2,/home/user/.chef/key.pem2


If there are manual nodes and edges you want to include in the graph, you can optionally create an input file.  I advise against this ; manual entries are future prob
lems.  For those who proceed nonetheless, the syntax of the input file can be found in SAMPLE_INPUT.txt.

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
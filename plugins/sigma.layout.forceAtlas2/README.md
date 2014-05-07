sigma.layout.forceAtlas2
========================

Plugin developed by [Mathieu Jacomy](https://github.com/jacomyma).

---

This plugin adds two methods to every sigma instances, `startForceAtlas2` and `stopForceAtlas2`. It implements [ForceAtlas2](http://webatlas.fr/tempshare/ForceAtlas2_Paper.pdf), a force-directed layout algorithm.

Basically, `myInstance.startForceAtlas2()` will start running the layout: It will spawn an atomic job in [conrad](http://jacomyal.github.io/conrad.js/) that will compute the next step. When the job is done, the `refresh` method of the instance will be called, and everything starts again.

The layout will not stop by itself, and it is necessary to call `myInstance.stopForceAtlas2()`.

The following lines demonstrate an example of how to integrate forceAtlas2 with sigma.parsers.json:

````html
<html>
<head>
<style type="text/css">
  #container {
    max-width: 1000px;
    height: 1000px;
    margin: auto;
  }
</style>
</head>
<body>
<div id="container"></div>
<script src="jquery_base_url/jquery.js"></script>
<script src="sigma_js_base_url/sigma.min.js"></script>
<script src="sigma_js_base_url/plugins/sigma.parsers.json.min.js"></script>
<script src="sigma_js_base_url/plugins/sigma.layout.forceAtlas2.min.js"></script>
<script>
$(document).ready(function(){ // jquery to assure that the DOM is fully loaded
    var sigmaInst =  new sigma({
  	    container: 'container',
  	    settings: {
  	        defaultNodeColor: '#ec5148'
  	    }
    });

    // use any server-side language to generate your json graph data; in this case, it's PHP
    sigma.parsers.json(
        'http://myUrl/jsonData.php', 
        sigmaInst, 
        function() {  // callback function called after loading json data 
        		sigmaInst.refresh();
        		sigmaInst.startForceAtlas2();    		
        }
    );
});
</script>
</body>
</html>
````

jsonData.php template:

````PHP
/*
  query your preferred database for fetching nodes and edges
  
  make sure your data are encoded in UTF-8
*/
while($node = mysql_fetch_array($handle_nodes)){
	$nodes[] = array("id"=>$node['id'], "x"=> (float)rand()/(float)getrandmax(), "y"=>(float)rand()/(float)getrandmax(), "label"=>$node['description'], "size"=>2);
}

$edges = array();
while($edge = mysql_fetch_array($handle_edges)){
	$edges[] = array("id"=>$edge['node_a_id'].'-'.$edge['node_b_id'], "source"=>$edge['node_a_id'], "target"=>$edge['node_b_id']);
}
echo json_encode(array("nodes"=>$nodes, "edges"=>$edges));
````





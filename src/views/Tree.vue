<template>
  <div class="tree">
    <h1>This is an tree page</h1>
  </div>
</template>

<script>

function makeNode(name, children){
  let node = {name};
  if(Array.isArray(children)) node.children = children;
  return node;
}


var tree = makeNode('www',[
  makeNode('etc',[
    makeNode('apache'),
    makeNode('nginx',[makeNode('file.config')]),
    makeNode('consul',[makeNode('config.json'),makeNode('data')])
  ]),
  makeNode('hosts'),
  makeNode('readme')
]);

function showNodes (tree, d=0){
  let {name,children} = tree;
  console.log('---|'.repeat(d),name);
  if(children) children.forEach(v => showNodes(v,d+1));
}

function filterNodes(fn,tree){
  let {name, children} = tree;

  function filter(list){
    let out =[];
    list.forEach((c) => {
      let filteChildren = Array.isArray(c.children) && filter(c.children);
      if(fn(c) || filteChildren) out.push({name:c.name, children:filteChildren});
    });
    return out.length ? out : false;
  }

  return children.length ? {name, children:filter(children)} : (fn(tree) ? {name} : {});
}

function searchNode (node,tree){
  let {children} = tree, index;
  if(children){
    index = children.indexOf(node);
    if(~index) 
      return [children,index];
    else 
      return children.reduce((t,n) =>{let r = searchNode(node,n); return r? t.concat(r) : t}, []);
  }
}

function deleteNode(node,tree){
  let index = searchNode(node, tree);
  return index.length ? index[0].splice(index[1], 1) && tree : false;
}

function addNode(newNode,tree){
  tree.children.push(newNode);
  return tree;
}

showNodes(tree);
showNodes(filterNodes(({name})=>name.includes('m'), tree));
var node = tree.children[0].children[0];
showNodes(tree);
deleteNode(node,tree);
showNodes(tree);
addNode(node,tree.children[0]);
showNodes(tree);


export default {
  
}
</script>

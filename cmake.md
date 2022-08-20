# CMake

## Generate and view dependency graph

Create dot files.

```bash
cmake .. --graphviz=viz/viz.dot
```

Visualize using `Graphviz Interactive Preview` by `tintinweb`.
Output to SVG or PNG.

```bash
dot -Tsvg viz.dot > ../viz.svg
dot -Tpng viz.dot > ../viz.png
```

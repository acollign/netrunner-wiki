# Externs

Whenever the frontend JavaScript dependencies change (e.g. if one is updated or a new one is added), the externs also need to be updated.


## File Structure

For each top level export used by the cljs, a file exists in `src/clsj/externs` named after the export (e.g. `$.js` for jQuery's `$`). There is an additional one named `extras.js` that contains the extra globals defined in the netrunner code.

These files are referenced in the compilation settings in the `project.clj`, and if any new ones are created (or removed), they will need to be added to (or removed from) the `:externs` array.


## Generating

Externs for dependency libraries have been generated using http://jmmk.github.io/javascript-externs-generator/.

To use the generator, first start the netrunner frontend server locally. Then load all of the external libraries into the generator using their paths from `layout.pug` (e.g. `http://localhost:1042/lib/jquery/jquery.min.js`), and press "Load". Once they are all loaded, type in the global to export (the one used by the `cljs` code, e.g. `$`), and press "Extern!". Copy the result and save it to a file named after the global (see "File Structure" above). Repeat for the remaining globals.

Remember to update the `project.clj` if there are any new (or removed) files.


## Troubleshooting

### I can't copy the "Externed Namespaces"

There have been issues copying out of the text box directly, but if you use the browser's inspector to find the text box, and copy from there, the data is available. You may need to refresh the browser and reload the libraries between generations, however. Note that some globals are modified by multiple libraries (looking at you `$`), so make sure to import everything that affects a global before exporting. Safest is to just import everything.
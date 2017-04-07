# api documentation for  [sprintf (v0.1.5)](https://github.com/maritz/node-sprintf)  [![npm package](https://img.shields.io/npm/v/npmdoc-sprintf.svg?style=flat-square)](https://www.npmjs.org/package/npmdoc-sprintf) [![travis-ci.org build-status](https://api.travis-ci.org/npmdoc/node-npmdoc-sprintf.svg)](https://travis-ci.org/npmdoc/node-npmdoc-sprintf)
#### Sprintf() for node.js

[![NPM](https://nodei.co/npm/sprintf.png?downloads=true)](https://www.npmjs.com/package/sprintf)

[![apidoc](https://npmdoc.github.io/node-npmdoc-sprintf/build/screenCapture.buildNpmdoc.browser._2Fhome_2Ftravis_2Fbuild_2Fnpmdoc_2Fnode-npmdoc-sprintf_2Ftmp_2Fbuild_2Fapidoc.html.png)](https://npmdoc.github.io/node-npmdoc-sprintf/build/apidoc.html)

![npmPackageListing](https://npmdoc.github.io/node-npmdoc-sprintf/build/screenCapture.npmPackageListing.svg)

![npmPackageDependencyTree](https://npmdoc.github.io/node-npmdoc-sprintf/build/screenCapture.npmPackageDependencyTree.svg)



# package.json

```json

{
    "author": {
        "name": "Moritz Peters"
    },
    "bugs": {
        "url": "https://github.com/maritz/node-sprintf/issues"
    },
    "dependencies": {},
    "description": "Sprintf() for node.js",
    "devDependencies": {
        "nodeunit": "0.8.0"
    },
    "directories": {
        "lib": "./lib"
    },
    "dist": {
        "shasum": "8f83e39a9317c1a502cb7db8050e51c679f6edcf",
        "tarball": "https://registry.npmjs.org/sprintf/-/sprintf-0.1.5.tgz"
    },
    "engines": {
        "node": ">=0.2.4"
    },
    "gitHead": "28eec6861288ba7ac6446aa221eea2b8ebdb0381",
    "homepage": "https://github.com/maritz/node-sprintf",
    "license": "BSD-3-Clause",
    "main": "./lib/sprintf",
    "maintainers": [
        {
            "name": "maritz",
            "email": "moritz@mpeters.biz"
        }
    ],
    "name": "sprintf",
    "optionalDependencies": {},
    "readme": "ERROR: No README data found!",
    "repository": {
        "type": "git",
        "url": "git+https://github.com/maritz/node-sprintf.git"
    },
    "scripts": {
        "test": "nodeunit test/function-export.js"
    },
    "version": "0.1.5"
}
```



# <a name="apidoc.tableOfContents"></a>[table of contents](#apidoc.tableOfContents)

#### [module sprintf](#apidoc.module.sprintf)
1.  [function <span class="apidocSignatureSpan"></span>sprintf ()](#apidoc.element.sprintf.sprintf)
1.  [function <span class="apidocSignatureSpan">sprintf.</span>format (parse_tree, argv)](#apidoc.element.sprintf.format)
1.  [function <span class="apidocSignatureSpan">sprintf.</span>object_stringify (obj, depth, maxdepth, seen)](#apidoc.element.sprintf.object_stringify)
1.  [function <span class="apidocSignatureSpan">sprintf.</span>parse (fmt)](#apidoc.element.sprintf.parse)
1.  [function <span class="apidocSignatureSpan">sprintf.</span>vsprintf (fmt, argv)](#apidoc.element.sprintf.vsprintf)
1.  object <span class="apidocSignatureSpan">sprintf.</span>cache



# <a name="apidoc.module.sprintf"></a>[module sprintf](#apidoc.module.sprintf)

#### <a name="apidoc.element.sprintf.sprintf"></a>[function <span class="apidocSignatureSpan"></span>sprintf ()](#apidoc.element.sprintf.sprintf)
- description and source-code
```javascript
sprintf = function () {
		if (!str_format.cache.hasOwnProperty(arguments[0])) {
			str_format.cache[arguments[0]] = str_format.parse(arguments[0]);
		}
		return str_format.format.call(null, str_format.cache[arguments[0]], arguments);
	}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.sprintf.format"></a>[function <span class="apidocSignatureSpan">sprintf.</span>format (parse_tree, argv)](#apidoc.element.sprintf.format)
- description and source-code
```javascript
format = function (parse_tree, argv) {
		var cursor = 1, tree_length = parse_tree.length, node_type = '', arg, output = [], i, k, match, pad, pad_character, pad_length
;
		for (i = 0; i < tree_length; i++) {
			node_type = get_type(parse_tree[i]);
			if (node_type === 'string') {
				output.push(parse_tree[i]);
			}
			else if (node_type === 'array') {
				match = parse_tree[i]; // convenience purposes only
				if (match[2]) { // keyword argument
					arg = argv[cursor];
					for (k = 0; k < match[2].length; k++) {
						if (!arg.hasOwnProperty(match[2][k])) {
							throw new Error(sprintf('[sprintf] property "%s" does not exist', match[2][k]));
						}
						arg = arg[match[2][k]];
					}
				}
				else if (match[1]) { // positional argument (explicit)
					arg = argv[match[1]];
				}
				else { // positional argument (implicit)
					arg = argv[cursor++];
				}

				if (/[^sO]/.test(match[8]) && (get_type(arg) != 'number')) {
					throw new Error(sprintf('[sprintf] expecting number but found %s "' + arg + '"', get_type(arg)));
				}
				switch (match[8]) {
					case 'b': arg = arg.toString(2); break;
					case 'c': arg = String.fromCharCode(arg); break;
					case 'd': arg = parseInt(arg, 10); break;
					case 'e': arg = match[7] ? arg.toExponential(match[7]) : arg.toExponential(); break;
					case 'f': arg = match[7] ? parseFloat(arg).toFixed(match[7]) : parseFloat(arg); break;
				    case 'O': arg = str_format.object_stringify(arg, 0, parseInt(match[7]) || 5); break;
					case 'o': arg = arg.toString(8); break;
					case 's': arg = ((arg = String(arg)) && match[7] ? arg.substring(0, match[7]) : arg); break;
					case 'u': arg = Math.abs(arg); break;
					case 'x': arg = arg.toString(16); break;
					case 'X': arg = arg.toString(16).toUpperCase(); break;
				}
				arg = (/[def]/.test(match[8]) && match[3] && arg >= 0 ? '+'+ arg : arg);
				pad_character = match[4] ? match[4] == '0' ? '0' : match[4].charAt(1) : ' ';
				pad_length = match[6] - String(arg).length;
				pad = match[6] ? str_repeat(pad_character, pad_length) : '';
				output.push(match[5] ? arg + pad : pad + arg);
			}
		}
		return output.join('');
	}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.sprintf.object_stringify"></a>[function <span class="apidocSignatureSpan">sprintf.</span>object_stringify (obj, depth, maxdepth, seen)](#apidoc.element.sprintf.object_stringify)
- description and source-code
```javascript
object_stringify = function (obj, depth, maxdepth, seen) {
		var str = '';
		if (obj != null) {
			switch( typeof(obj) ) {
			case 'function':
				return '[Function' + (obj.name ? ': '+obj.name : '') + ']';
			    break;
			case 'object':
				if ( obj instanceof Error) { return '[' + obj.toString() + ']' };
				if (depth >= maxdepth) return '[Object]'
				if (seen) {
					// add object to seen list
					seen = seen.slice(0)
					seen.push(obj);
				}
				if (obj.length != null) { //array
					str += '[';
					var arr = []
					for (var i in obj) {
						if (seen && seen.indexOf(obj[i]) >= 0) arr.push('[Circular]');
						else arr.push(str_format.object_stringify(obj[i], depth+1, maxdepth, seen));
					}
					str += arr.join(', ') + ']';
				} else if ('getMonth' in obj) { // date
					return 'Date(' + obj + ')';
				} else { // object
					str += '{';
					var arr = []
					for (var k in obj) {
						if(obj.hasOwnProperty(k)) {
							if (seen && seen.indexOf(obj[k]) >= 0) arr.push(k + ': [Circular]');
							else arr.push(k +': ' +str_format.object_stringify(obj[k], depth+1, maxdepth, seen));
						}
					}
					str += arr.join(', ') + '}';
				}
				return str;
				break;
			case 'string':				
				return '"' + obj + '"';
				break
			}
		}
		return '' + obj;
	}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.sprintf.parse"></a>[function <span class="apidocSignatureSpan">sprintf.</span>parse (fmt)](#apidoc.element.sprintf.parse)
- description and source-code
```javascript
parse = function (fmt) {
		var _fmt = fmt, match = [], parse_tree = [], arg_names = 0;
		while (_fmt) {
			if ((match = /^[^\x25]+/.exec(_fmt)) !== null) {
				parse_tree.push(match[0]);
			}
			else if ((match = /^\x25{2}/.exec(_fmt)) !== null) {
				parse_tree.push('%');
			}
			else if ((match = /^\x25(?:([1-9]\d*)\$|\(([^\)]+)\))?(\+)?(0|'[^$])?(-)?(\d+)?(?:\.(\d+))?([b-fosOuxX])/.exec(_fmt)) !== null
) {
				if (match[2]) {
					arg_names |= 1;
					var field_list = [], replacement_field = match[2], field_match = [];
					if ((field_match = /^([a-z_][a-z_\d]*)/i.exec(replacement_field)) !== null) {
						field_list.push(field_match[1]);
						while ((replacement_field = replacement_field.substring(field_match[0].length)) !== '') {
							if ((field_match = /^\.([a-z_][a-z_\d]*)/i.exec(replacement_field)) !== null) {
								field_list.push(field_match[1]);
							}
							else if ((field_match = /^\[(\d+)\]/.exec(replacement_field)) !== null) {
								field_list.push(field_match[1]);
							}
							else {
								throw new Error('[sprintf] ' + replacement_field);
							}
						}
					}
					else {
                        throw new Error('[sprintf] ' + replacement_field);
					}
					match[2] = field_list;
				}
				else {
					arg_names |= 2;
				}
				if (arg_names === 3) {
					throw new Error('[sprintf] mixing positional and named placeholders is not (yet) supported');
				}
				parse_tree.push(match);
			}
			else {
				throw new Error('[sprintf] ' + _fmt);
			}
			_fmt = _fmt.substring(match[0].length);
		}
		return parse_tree;
	}
```
- example usage
```shell
n/a
```

#### <a name="apidoc.element.sprintf.vsprintf"></a>[function <span class="apidocSignatureSpan">sprintf.</span>vsprintf (fmt, argv)](#apidoc.element.sprintf.vsprintf)
- description and source-code
```javascript
vsprintf = function (fmt, argv) {
	var argvClone = argv.slice();
	argvClone.unshift(fmt);
	return sprintf.apply(null, argvClone);
}
```
- example usage
```shell
n/a
```



# misc
- this document was created with [utility2](https://github.com/kaizhu256/node-utility2)

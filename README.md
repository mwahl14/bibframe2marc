# bibframe2marc-xsl

XSLT 1.0 conversion from RDF/XML [BIBFRAME 2.0](http://www.loc.gov/bibframe/) to [MARCXML](http://www.loc.gov/marcxml/).

## Introduction

_bibframe2marc-xsl_ consists of an [XSLT 1.0 stylesheet](src/compile.xsl) that takes a set of [XML rules](rules) and compiles them into another stylesheet (bibframe2marc.xsl). The conversion stylesheet takes an RDF/XML document representing a single BIBFRAME 2.0 description and converts it to a MARCXML document. The bibframe2marc.xsl stylesheet can be used as part of a conversion pipeline, as for example with the [Biblio::BF2MARC](https://github.com/lcnetdev/biblio-bf2marc) perl library.

## Dependencies

### Build dependencies

* [libxslt](http://xmlsoft.org/XSLT) -- specifically `xsltproc` -- is used by the root level `Makefile` to construct the `bibframe2marc.xsl` conversion stylesheet from the rules in the [rules](rules) subdirectory. Any XSLT 1.0 processor should be able to build the conversion stylesheet.

* [XSpec](https://github.com/xspec/xspec) is the test framework for both the rules compiler and the conversion spreadsheet. It is used by the root level `Makefile` for the `test` targets.

* A Java JRE and the Saxon XSLT and XQuery processor are required by XSpec. For more information, see the installation pages on the [XSpec wiki](https://github.com/xspec/xspec/wiki).

## Usage

### Building

`make` in the root level of the working directory will create the `bibframe2marc.xsl` conversion stylesheet from the rules in the `rules` subdirectory. The destination stylesheet filename and path can be configured with the `TARGET_XSL` variable.

### Using the generated conversion stylesheet (bibframe2marc.xsl)

The `bibframe2marc.xsl` conversion stylesheet is an XSLT 1.0 application that converts a striped RDF/XML document containing a single BIBFRAME 2.0 "description" (defined as an RDF graph composed of two top level nodes that refer to each other, one `bf:Instance` node and one `bf:Work` node) into a MARCXML document. It can be invoked as a standalone application using an XSLT 1.0 processor such as `xsltproc`, or it can be embedded in another application using a library such as `libxslt` for processing, as with the [Biblio::BF2MARC](https://github.com/lcnetdev/biblio-bf2marc) perl library.

For more information about what consitutes a BIBFRAME description, see the [design notes](doc/design.md).

The converions stylesheet takes the following parameters:

* `pRecordId` -- an internal system record ID for use (for example) in a MARC 001 control field. If `pRecordId` is not provided, the conversion will use the `generate-id()` function to generate a record ID.

* `pGenerationTimestamp` -- a timestamp for the conversion. If it is not provided, and if the `date:date-time()` function is available, it will be created from the value of `date:date-time()`.

### Using the compiler stylesheet (src/compile.xsl)

The conversion stylesheet is generated from the rules in the `rules` subdirectory by the compiler stylesheet `src/compile.xsl`. You can adapt the sample conversion provided to your own needs, or create your own conversion rules. For more information, see the [conversion rules documentation](doc/rules.md).

To build a conversion stylesheet from a rules file, you can just run the compiler stylesheet with an XSLT 1.0 processor. For example, using `xsltproc`:

```
xsltproc src/compile.xsl rules.xml > bibframe2marc.xsl
```

### Tests

The compiler stylesheet has XSpec tests written for it that can be run with `make test_compile` (assuming that XSpec is installed and configured). The tests are in the `tests` subdirectory.

In addition, the rules in the `rules` subdirectory can be tested using XSpec with `make test_rules`. Rules tests are in the `rules/tests` subdirectory.

The `test` target of the Makefile runs both `test_compile` and `test_rules`.

## TODO

* A formal XML schema for the conversion rules still needs to be written

## Known issues

* The rules compiler uses the `xsl:namespace-alias` element of XSLT to allow for generating a conversion stylesheet. The behavior of this element differs depending on your XSLT processor. libxslt (xsltproc) uses the same namespace prefix in the source stylesheet as the namespace prefix in the output stylesheet, switching the namespace underneath. The Saxon XSLT processor switches out both the namespace prefix and the namespace itself. This results in slightly different output stylesheets -- but the actual BIBFRAME to MARC transformation is exactly the same. `compile.xsl` has been written to work most smoothly with `xsltproc`.

## See also

* [Issue tracker](https://github.com/lcnetdev/bibframe2marc-xsl/issues) on GitHub
* [Conversion rules documentation](doc/rules.md)
* [Design notes](doc/design.md)
* [Biblio::BF2MARC](https://github.com/lcnetdev/biblio-bf2marc) -- a perl library that uses _bibframe2marc-xsl_ for BIBFRAME to MARC conversion
* The [Bibliographic Framework Initiative](http://www.loc.gov/bibframe/) at the Library of Congress
* The MARC to BIBFRAME conversion tool ([marc2bibframe2](https://github.com/lcnetdev/marc2bibframe2))

<!DOCTYPE html>
<html>
  <head>
    <meta charset='utf-8'>
    <meta http-equiv="X-UA-Compatible" content="chrome=1">
    <link href='https://fonts.googleapis.com/css?family=Chivo:900' rel='stylesheet' type='text/css'>
    <link rel="stylesheet" type="text/css" href="stylesheets/stylesheet.css" media="screen">
    <link rel="stylesheet" type="text/css" href="stylesheets/pygment_trac.css" media="screen">
    <link rel="stylesheet" type="text/css" href="stylesheets/print.css" media="print">
    <!--[if lt IE 9]>
    <script src="//html5shiv.googlecode.com/svn/trunk/html5.js"></script>
    <![endif]-->
    <title>php-marcspec by cKlee</title>
  </head>

  <body>
    <div id="container">
      <div class="inner">

        <header>
          <h1>PHP MARCspec</h1>
          <h2>A MARCspec parser and validator</h2>
        </header>

        <section id="downloads" class="clearfix">
          <a href="https://github.com/cKlee/php-marc-spec/zipball/master" id="download-zip" class="button"><span>Download .zip</span></a>
          <a href="https://github.com/cKlee/php-marc-spec/tarball/master" id="download-tar-gz" class="button"><span>Download .tar.gz</span></a>
          <a href="https://github.com/cKlee/php-marc-spec" id="view-on-github" class="button"><span>View on GitHub</span></a>
        </section>

        <hr>

        <section id="main_content">

For currently supported version of **MARCspec - A common MARC record path language** see http://cklee.github.io/marc-spec/marc-spec-e66931e.html .


### Usage

```php
<?php

namespace CK\MARCspec;
require("vendor/autoload.php");

// parse and access MARCspec like an array
$fixed = new MARCspec('007[0]/1-8{/0=\a}');
echo $fixed['field']['tag'];                                                  // '007'
echo $fixed['field']['charStart'];                                            // 1
echo $fixed['field']['charEnd'];                                              // 8
echo $fixed['field']['charLength'];                                           // 8
echo $fixed['field']['subSpecs'][0]['leftSubTerm'];                           // '007[0]/0'
echo $fixed['field']['subSpecs'][0]['operator'];                              // '='
echo $fixed['field']['subSpecs'][0]['rightSubTerm'];                          // '\a'
echo $fixed['field']['subSpecs'][0]['rightSubTerm']['comparable'];            // 'a'

echo $fixed;                                                                  // '007[0]/1-8{007[0]/0=\a}'

$variable = new MARCspec('245_10$a');
echo $variable['field']['tag'];                                               // '245'
echo $variable['field']['indicator1'];                                        // '1'
echo $variable['field']['indicator2'];                                        // '0'
echo $variable['subfields'][0]['tag'];                                        // 'a'
echo $variable['a'][0]['tag'];                                                // 'a'

echo $variable;                                                               // '245_10$a'

$complex = new MARCspec('020$a{$q[0]~\pbk}{$c/0=\€|$c/0=\$}');
echo $complex['field']['tag'];                                                // '020'
echo $complex['subfields'][0]['tag'];                                         // 'a'

echo $complex['a'][0]['subSpecs'][0]['leftSubTerm'];                          // '020$q[0]'
echo $complex['a'][0]['subSpecs'][0]['operator'];                             // '~'
echo $complex['a'][0]['subSpecs'][0]['rightSubTerm']['comparable'];           // 'pbk'

echo $complex['a'][0]['subSpecs'][1][0]['leftSubTerm'];                       // '020$c/0'
echo $complex['a'][0]['subSpecs'][1][0]['leftSubTerm']['c'][0]['charStart'];  // 0
echo $complex['a'][0]['subSpecs'][1][0]['leftSubTerm']['c'][0]['charEnd'];    // null
echo $complex['a'][0]['subSpecs'][1][0]['leftSubTerm']['c'][0]['charLength']; // 1
echo $complex['a'][0]['subSpecs'][1][0]['operator'];                          // '='
echo $complex['a'][0]['subSpecs'][1][0]['rightSubTerm']['comparable'];        // '€'

echo $complex['a'][0]['subSpecs'][1][1]['leftSubTerm'];                       // '020$c/0'
echo $complex['a'][0]['subSpecs'][1][1]['leftSubTerm']['c'][0]['charStart'];  // 0
echo $complex['a'][0]['subSpecs'][1][1]['leftSubTerm']['c'][0]['charEnd'];    // null
echo $complex['a'][0]['subSpecs'][1][1]['leftSubTerm']['c'][0]['charLength']; // 1
echo $complex['a'][0]['subSpecs'][1][1]['operator'];                          // '='
echo $complex['a'][0]['subSpecs'][1][1]['rightSubTerm']['comparable'];        // '$'

// creating MARCspec

// creating a new Field
$Field = new Field('...');
$Field['indicator2'] = '1'; // or $Field->setIndicator2('1');
$Field['indexStart'] = 0; // or $Field->setIndex(0);

// creating a new MARCspec by setting the Field
$MARCspec = MARCspec::setField($Field);

// creating a new Subfield
$Subfield = new Subfield('a');

// adding the Subfield to the MARCspec
$MARCspec->addSubfields($Subfield);

// creating instances of MARCspec and ComparisonString
$LeftSubTerm = new MARCspec('...$a/#');
$RightSubTerm = new ComparisonString(',');

// creating a new SubSpec with instances above and an operator '='
$SubSpec = new SubSpec($LeftSubTerm,'=',$RightSubTerm);

// adding the SubSpec to the Subfield
$Subfield['subSpecs'] = $SubSpec;

// echo whole MARCspec
echo $MARCspec; // '...[0]__1$a{...$a/#=\,}' 
```

### ArrayAccess vs. Methods

MARCspec can be accessed like an immutable array with the following offsets or with its correponding methods (see source code for documentation of all methods).

#### Instances of MARCspec

| offset    | method get    | method set   | type  |
|:---------:|:-------------:|:------------:|:-----:|
| field     | getField      | setField     | Field |
| subfields | getSubfields  | addSubfields | array\[Subfield] |
| \[subfield tag] | getSubfield |          | array\[Subfield] |

#### Instances of Field

| offset    | method get    | method set    | type  |
|:---------:|:-------------:|:-------------:|:-----:|
| tag       | getTag        | setTag        | string |
| indicator1| getIndicator1 | setIndicator1 | string |
| indicator2| getIndicator2 | setIndicator2 | string |
| charStart | getCharStart  | setCharStart  | int |
| charEnd   | getCharEnd    | setCharEnd    | int |
| charLength| getCharLength |               | int |
| indexStart| getIndexStart | setIndexStart | int |
| indexEnd  | getIndexEnd   | setIndexEnd   | int |
| indexLength| getIndexLength |             | int |
| subSpecs  | getSubSpecs   | addSubSpecs   | array\[SubSpec]&#124;array\[array\[SubSpec]] |

#### Instances of Subfield

| offset    | method get    | method set    | type  |
|:---------:|:-------------:|:-------------:|:-----:|
| tag       | getTag        | setTag        | string |
| charStart | getCharStart  | setCharStart  | int |
| charEnd   | getCharEnd    | setCharEnd    | int |
| charLength| getCharLength |               | int |
| indexStart| getIndexStart | setIndexStart | int |
| indexEnd  | getIndexEnd   | setIndexEnd   | int |
| indexLength| getIndexLength |             | int |
| subSpecs  | getSubSpecs   | addSubSpecs   | array\[SubSpec]&#124;array\[array\[SubSpec]] |

#### Instances of ComparisonString

| offset    | method get    | type  |
|:---------:|:-------------:|:-----:|
| raw       | getRaw        | string |
| comparable| getComprable  | string |

#### Instances of SubSpec

| offset       | method get      | type  |
|:------------:|:---------------:|:-----:|
| leftSubTerm  | getLeftSubTerm  | MARCspec&#124;ComparisonString |
| operator     | getOperator     | string |
| rightSubTerm | getRightSubTerm | MARCspec&#124;ComparisonString |

        </section>

        <footer>
          php-marcspec is maintained by <a href="https://github.com/cKlee">cKlee</a><br>
          This page was generated by <a href="http://pages.github.com">GitHub Pages</a>. Tactile theme by <a href="https://twitter.com/jasonlong">Jason Long</a>.
        </footer>

        
      </div>
    </div>
  </body>
</html>

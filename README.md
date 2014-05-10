# Citation

[![Build Status](https://travis-ci.org/unitedstates/citation.svg?branch=master)](https://travis-ci.org/unitedstates/citation)

A fast, stand-alone legal citation extractor.

Currently supports:

* US Code
* US Public and Private Laws (slip laws)
* US Statutes at Large
* DC Code
* DC Register
* Judicial citations (using [walverine](https://github.com/adelevie/walverine))

Compatible in-browser with modern Firefox, Chrome, and IE **9+**.

## Install

[Install Node.js and NPM](http://nodejs.org/#download), then install Citation globally (may require `sudo`):

```bash
npm install -g citation
```

Or install it locally to a `node_modules` directory with `npm install citation`.

## [Javascript API Documentation](JS_API.md)

## [HTTP API Documentation](HTTP_API.md)

Citation provides a server interface for HTTP requests, returning JSON data
for specific citations.

## Command line

The shell command can accept a string to parse as an argument, through STDIN,
or from a file. It can output results to STDOUT, or to a file.

```bash
cite "section 5362(5) of title 31"

echo "section 5362(5) of title 31" | cite

cite --input=in-file.txt --output=out-file.json

cite "pursuant to 5 U.S.C. 552(a)(1)(E) and"
```

### Options

* `--input`: Filename to read text from
* `--output`: Filename to output text to
* `--pretty`: Prettify (indent) output
* `--types`: Limit citation types to a comma-separated list (e.g. "usc,law")

## Server deployment

See [etc/](etc) for an example upstart script to keep `cite-server` running in production.

## Context-aware citation detection

In the JavaScript interface, you can pass optional `context`, an object with arbitrary
key/value pairs, that can tell the citator what you already know about
the source text, and potentially allow more permissive detection.

For example, most DC legal documents use the prefix "D.C. Official Code"
before they cite the DC Code. But cross-references inside the DC Code to
other parts of the DC Code do not include this prefix. If you know that your
source text is the DC Code, you can detect cross-references by providing
a `source` value of "dc_code":

```javascript
Citation.find("required under § 3-101.01(13)(e), the Commission shall perform the", {
  context: {
    dc_code: {source: "dc_code"}
  }
})
```

Yields:

```json
[
  {
    "type": "dc_code",
    "match": "§ 3-101.01(13)(e)",
    "index": 15,

    "dc_code": {
      "id": "dc-code/3/101.01/13/e",
      "section_id": "dc-code/3/101.01",
      "title": "3",
      "section": "101.01",
      "subsections": ["13", "e"]
    }
  }
]
```

### Tests

This project is tested with [nodeunit](https://github.com/caolan/nodeunit).

To run tests, you'll need to install this project from source and install its
node dependencies:

```
git clone git@github.com:unitedstates/citation.git
cd citation
npm install
npm test
```

Test cases are stored in the `test` directory. Each test case covers a subsection
of the code and ensures that citations are correctly detected: for instance, see
[test/stat.js](test/stat.js).

To run all tests:

```bash
nodeunit test
```

To run a specific test:

```bash
nodeunit test/usc.js
```

## Public domain

This project is [dedicated to the public domain](LICENSE). As spelled out in [CONTRIBUTING](CONTRIBUTING.md):

> The project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](http://creativecommons.org/publicdomain/zero/1.0/).

> All contributions to this project will be released under the CC0 dedication. By submitting a pull request, you are agreeing to comply with this waiver of copyright interest.

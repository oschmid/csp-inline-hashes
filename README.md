# CSP Inline Hashes

Hugo module for creating hashes of inline scripts/styles and writing them to a `Content-Security-Policy` header.

## Import

Update your hugo.yaml to include this module:
```yaml
module:
  imports:
    disable: false
    path: "github.com/oschmid/csp-inline-hashes"
```
Then run:
```shell
hugo mod init <path to your repo> # if you haven't already
hugo mod get github.com/oschmid/csp-inline-hashes
hugo mod tidy
```

## Configuration

This module uses the following hugo.yaml parameters:
```yaml
params:
  cspInlineHashes:
    minify: "auto" # "always|never|auto" (default is auto, which will minify if 'hugo.Environment = "production"')
    algo: "sha256" # "sha256|sha384|sha512" (default is sha256)
outputs:
  home:
    - HTML
    - RSS
    - HEADERS # must be added after HTML
outputFormats:
  HEADERS:
    baseName: "_headers" # used by Netlify/Cloudflare
    isPlainText: true
    notAlternative: true
```

## Usage

In an HTML file where you have an inline script/style like this:
```html
<script>
function printHelloWorld() {
    console.log("hello world");
}
</script>
<style>
    body {
        background-color: red;
    }
</style>
```
replace it with the following:
```
{{ with partial "script.html" }}
    function printHelloWorld() {
        console.log("hello world");
    }
{{ end }}
{{ with partial "style.html"
    body {
        background-color: red;
    }
 {{ end }}
```
Then where you configure your headers add the following:
```
{{ $scriptHashes := partial "scriptHashes.html" -}}
{{ $styleHashes := partial "styleHashes.html" -}}
Content-Security-Policy: script-src-elem {{ $scriptHashes }}; style-src-elem {{ $styleHashes }}
```
See [demo](https://github.com/oschmid/csp-inline-hashes-demo) for a Netlify example.

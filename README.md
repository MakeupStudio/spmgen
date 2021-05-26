# SPMGen

[![SwiftPM 5.3](https://img.shields.io/badge/swiftpm-5.3-ED523F.svg?style=flat)](https://swift.org/download/) [![iOS](https://img.shields.io/badge/iOS-supported-brightgreen)]() [![macOS](https://img.shields.io/badge/macOS-supported-brightgreen)]() [![@maximkrouk](https://img.shields.io/badge/contact-@maximkrouk-1DA1F2.svg?style=flat&logo=twitter)](https://twitter.com/maximkrouk)

Resources boilerplate code generator for Swift.

## About

SPMGen provides static resource factories for various resource types.

Supported resources:

| Resource           | Extensions        | Is reliable |
| ------------------ | ----------------- | ----------- |
| ColorResource      | `.xcassets`       | true        |
| FontResource       | `.ttf` `.otf`     | true        |
| ImageResource      | `.xcassets`       | true        |
| NibResource        | `.xib`            | not used    |
| StoryboardResource | `.storyboard`     | not used    |
| SCNSceneResource   | `.scnassets/.scn` | true        |

## Setup

Install `spmgen` CLI

### Installation

#### Homebrew

```bash
brew install makeupstudio/formulae/spmgen
```

#### Makefile

```bash
# Download repo
git clone https://github.com/makeupstudio/spmgen.git

# Navigate to repo directory
cd spmgen

# Build and install using Make
make install

# You can also delete spmgen using `make uninstall` command
```

### Integration

Add SPMGen dependency to your package

```swift
.package(url: "https://github.com/makeupstudio/spmgen.git", from: "1.0.1")
```

Create `<#Project#>Resources` target with a following structure
> Exact structure is not required, but it's convenient enough and the code in sections below relies on it

```plaintext
Sources
  <#Project#>Resources
    Resources
      <#Assets#>
```

Specify resource processing and add SPMResources dependency to your target

```swift
.target(
  name: "<#Project#>Resources",
  dependencies: [
    .product(
      name: "SPMResources",
      package: "spmgen"
    )
  ],
  resources: [
    .process("Resources")
  ]
)
```

Add a script to your `Run Script` target build phases

```bash
spmgen resources "$SRCROOT/Sources/<#Project#>Resources/Resources" \
  --output "$SRCROOT/Sources/<#Project#>Resources/SPMGen.swift" \
  --indentor " " \
  --indentation-width 2

# You can also add `--disable-exports` flag to disable `@_exported` attribute
# for `import SPMResources` declaration in generated file
```

Add `<#Project#>Resources` target as a dependency to other targets

```swift
.target(
  name: "<#Project#>Module",
  dependencies: [
    .target(name: "<#Project#>Resources")
  ]
)
```

Also, it's recommended to add generated file to `.gitignore`

## Usage

Import your `<#Project#>Resources` package and initialize objects using `.resource()` static factory

```swift
import <#Project#>Resources
import UIKit

let label = UILabel()
label.backgroundColor = .resource(.accentColor)
label.textColor = .resource(.primaryText)
label.font = .primary(ofSize: 12, weight: .semibold, style: .italic)

let imageView = UIImageView(image: .resource(.logo))
```



> **Note: Fonts require additional setup**
>
> For example you want to add `Monsterrat` and `Arimo` fonts with different styles
>
> - Download fonts and add them to `Sources/<#Project#>Resources/Resources` folder
>
> - Add a static factories for your custom fonts (_[Example](https://gist.github.com/maximkrouk/5bcccc5db12f0347676be5a776c309a8)_)
> - Register custom fonts on app launch (_in AppDelegate, for example_) 
>   - `UIFont.bootstrap()` if you are using code from the example above.


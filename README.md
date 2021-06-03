This serverless application provides a Lambda Layer that includes the [*sharp*](https://sharp.pixelplumbing.com/) image processing library for Node.js.

The motivation for this layer is two-fold:

1. You need to bundle the x86 binaries for libvips with *sharp* when installing it, this is difficult on MacOS when using SAM.
2. The library size is big enough to make the function not editable in the Lambda console.

To use this layer in a Node.js lambda function, simply add this layer to your stack, for instance in a SAM template. The published version of this layer will track the version of *sharp* that is available, starting with 0.28.3.

```
Resources:

  SharpLambdaLayer:
    Type: AWS::Serverless::Application
    Properties:
      Location:
        ApplicationId: arn:aws:serverlessrepo:us-east-1:987481058235:applications/nodejs-sharp-lambda-layer
        SemanticVersion: 0.28.3

  SomeNodejsFunction:
    Type:
    Type: AWS::Serverless::Function
    Properties:
      Layers:
        - !GetAtt ["SharpLambdaLayer", "Outputs.LayerVersion"]
      ...
```

Add `sharp` to your `devDependencies` in your `package.json` so it doesn't get bundled for deployment, then use it normally in your code:

```
const axios = require('axios')
const sharp = require('sharp')

const buffer = await axios.get('https://cdn.jsdelivr.net/gh/lovell/sharp@master/docs/image/sharp-logo.svg', {responseType: 'arraybuffer'})
const image = await sharp(buffer).resize({width: 50}).toBuffer()

// profit!
```

# Tar Sadness

If you try to push this app to a Cloud Foundry using Diego, you will not
have a good time.

## Why is this sad?

This repo has a very long filename. When Diego sends the package to Garden
for staging, it uses [StreamIn](https://github.com/cloudfoundry-incubator/garden-linux/blob/8ab2cbf0696fd0b6bd1a0f15f2d3a463e4acbdac/linux_container/linux_container.go#L549-L594)
which sends the files over as a tar. The tar format has a restriction on maximum
file length, so fails to untar.

## Why is this _really_ sad?

As an app developer, the only information I get about the failure is "StagingError",
with no indication about what is wrong or how I can fix it. One has to SSH
into the Diego cell VM to look at the Garden logs in order to diagnose the
error, which shows up as `garden-linux.garden-server.stream-in.failed`.

## `cf push tar-sadness`

```
Creating app tar-sadness in org wendorf / space development as dwendorf@pivotal.io...
OK

Creating route tar-sadness.cfapps.io...
OK

Binding tar-sadness.cfapps.io to tar-sadness...
OK

Uploading tar-sadness...
Uploading app files from: /Users/pivotal/workspace/tar-sadness
Uploading 927B, 3 files
Done uploading
OK

Starting app tar-sadness in org wendorf / space development as dwendorf@pivotal.io...
Downloading python_buildpack...
Downloading php_buildpack...
Downloading binary_buildpack...
Downloading nodejs_buildpack...
Downloading java_buildpack...
Downloading ruby_buildpack...
Downloading go_buildpack...
Downloading liberty_buildpack...
Downloading staticfile_buildpack...
Downloaded php_buildpack
Downloaded python_buildpack
Downloaded liberty_buildpack
Downloaded nodejs_buildpack
Downloaded go_buildpack
Downloaded ruby_buildpack
Downloaded binary_buildpack
Downloaded java_buildpack
Downloaded staticfile_buildpack
Creating container
Successfully created container
Downloading app package...

FAILED
StagingError

TIP: use 'cf logs tar-sadness --recent' for more information
```

## `cf logs tar-sadness --recent`

```
Connected, dumping recent logs for app tar-sadness in org wendorf / space development as dwendorf@pivotal.io...

2016-05-27T09:27:26.77-0700 [API/5]      OUT Created app with guid 78e9519b-df2e-428b-9228-7e352076f500
2016-05-27T09:27:28.45-0700 [API/4]      OUT Updated app with guid 78e9519b-df2e-428b-9228-7e352076f500 ({"route"=>"bf74662a-69dc-4ecb-b45e-52ed9edacea1"})
2016-05-27T09:27:37.12-0700 [API/5]      OUT Updated app with guid 78e9519b-df2e-428b-9228-7e352076f500 ({"state"=>"STARTED"})
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading python_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading php_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading binary_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading nodejs_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading java_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading ruby_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading go_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading liberty_buildpack...
2016-05-27T09:27:37.38-0700 [STG/0]      OUT Downloading staticfile_buildpack...
2016-05-27T09:27:37.43-0700 [STG/0]      OUT Downloaded python_buildpack
2016-05-27T09:27:37.43-0700 [STG/0]      OUT Downloaded liberty_buildpack
2016-05-27T09:27:37.43-0700 [STG/0]      OUT Downloaded nodejs_buildpack
2016-05-27T09:27:37.43-0700 [STG/0]      OUT Downloaded php_buildpack
2016-05-27T09:27:37.43-0700 [STG/0]      OUT Downloaded go_buildpack
2016-05-27T09:27:37.43-0700 [STG/0]      OUT Downloaded ruby_buildpack
2016-05-27T09:27:37.44-0700 [STG/0]      OUT Downloaded binary_buildpack
2016-05-27T09:27:37.44-0700 [STG/0]      OUT Downloaded java_buildpack
2016-05-27T09:27:37.48-0700 [STG/0]      OUT Downloaded staticfile_buildpack
2016-05-27T09:27:37.48-0700 [STG/0]      OUT Creating container
2016-05-27T09:27:38.42-0700 [STG/0]      OUT Successfully created container
2016-05-27T09:27:38.42-0700 [STG/0]      OUT Downloading app package...
2016-05-27T09:27:38.59-0700 [API/6]      ERR Failed to stage application: staging failed
```

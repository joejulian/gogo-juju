# GO Library to use JUJU commands to bring up a Kubernetes cluster

### Pre-reqs
`go get gopkg.in/yaml.v2`

## Usage
Currently can bring up kubernetes cluster in CNCT maas lab or on aws.

You will need to set the instance of `Juju` with the following fields:

### Juju struct options
* Note - While several fields are optional, you must include either `MaasCl` __and__ `MaasCr` __or__ `AwsCl` __and__ `AwsCr`

| Field Name     | Required    | Type            | Description                             |
| -------------- | ----------- | --------------- | --------------------------------------- |
| Kind           | __Required__| String          |  must be "aws" or "maas"                |
| Name           | __Required__| String          |  used to set JUJU_DATA path             |
| Bundle         | __Required__| String          |  ex "cs:bundle/canonical-kubernetes-193"|
| p  						 |  Optional   | Parallel        | used for multiple cluster creation      |
| MaasCl         | Optional    | MaasCloud       | maas cloud details                      |
| MaasCr         | Optional    | MaasCredentials | maas credential details                 |
| Aws Cl         | Optional    | AwsCloud        | aws cloud details                       |
| AwsCr          | Optional    | AwsCredentials  | aws credential details                  |  

## MaasCl Options
| Field Name     | Required    | Type            | Description                             |
| -------------- | ----------- | --------------- | --------------------------------------- |
| Type           |__Required__ | String          | name for your maas cloud( dev, prod )   |
| Endpoint       |__Required__ | String          | maas url ex-"http://<ip>/MAAS/api/2.0"  |

## MaasCr Options
| Field Name     | Required    | Type            | Description                             |
| -------------- | ----------- | --------------- | --------------------------------------- |
| CloudName      | __Required__| String          | must match desired MaasCl.Type cloud    |
| Username       | __Required__| String          | maas username 													 |
| MaasOauth      | __Required__| String          | maas oauth info                         |

## AwsCl Options
| Field Name     | Required    | Type            | Description                             |
| -------------- | ----------- | --------------- | --------------------------------------- |
| Region         | Optional    | String          | ex "aws/us-west-2"                      |

## AwsCr Options
| Field Name     | Required    | Type            | Description                             |
| -------------- | ----------- | --------------- | --------------------------------------- |
| Username       | __Required__| String          | your aws username                       |
| AccessKey      | __Required__| String          | aws access key                          |
| SecretKey      | __Required__| String          | aws secret key                          |

### Working commands:

- `SetAWSCreds()` - sets aws credentials for use with juju
- `SetMAASCreds()` sets maas credentials for use with juju
- `SetMAASCloud()` - sets maas cloud information for use with juju
- `Spinup()` - will spinup cluster from specified creds/cloud/bundle
- `DisplayStatus()` - will display results of running juju status
- `ClusterReady()` - will return boolean corresponding to readiness of cluster
- `GetKubeConfig()` - will print out kubeconfig to stdout
- `DestroyCluster()` - will tear down juju controller and associated cluster


### Sample file to bring up maas cluster on Samsung's CNCT nuc lab:

```
package main

import (
	"github.com/dstorck/gogo"
)

var testRun = gogo.Juju{
	Kind:   "maas",
	Name:   "test-cluster",
	Bundle: "cs:bundle/kubernetes-core-306",
	MaasCl: myMaasCloud,
	MaasCr: myMaasCreds,
}

var myMaasCloud = gogo.MaasCloud{
	Type:     "lab",
	Endpoint: "<your-maas-url>",
}
var myMaasCreds = gogo.MaasCredentials{
	CloudName: "nuc-lab",
	Username:  "<username>",
	MaasOauth: "<maas-password>",
}

// current available commands, not meant to be run all at once
func main() {
  testRun.SetMAASCreds()
  testRun.SetMAASCloud()
  testRun.Spinup()
  testRun.DisplayStatus()
  testRun.ClusterReady()
  testRun.GetKubeConfig()
  testRun.DestroyCluster()
}
```

### Sample file to bring up aws cluster
```
package main

import (
	"github.com/dstorck/gogo"
)

var testRun = gogo.Juju{
	Kind:   "aws",
	Name:   "test-cluster",
	Bundle: "cs:bundle/kubernetes-core-306",
	AwsCr:  myAWScreds,
	AwsCl:  myAWScloud,
}

var myAWScreds = gogo.AWSCredentials{
	Username:  "<your-aws-username>",
	AccessKey: "<your-access-key>",
	SecretKey: "<your-secret-key",
}

var myAWScloud = gogo.AWSCloud{
	Region: "aws/us-west-2",
}

// currently available commands, not meant to be run all at once
func main() {
  // testRun.SetAWSCreds()
  // testRun.DisplayStatus()
  // testRun.Spinup()
  // testRun.ClusterReady()
  // testRun.GetKubeConfig()
  // testRun.DestroyCluster()
}
```
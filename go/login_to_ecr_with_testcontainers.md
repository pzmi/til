# Login to ECR with Testcontainers

Logging in to container registires with https://golang.testcontainers.org is not trivial as it's not documented very well. 

```go
import (
    "context"
    "encoding/base64"
    "encoding/json"
    "github.com/docker/docker/api/types"
    "github.com/testcontainers/testcontainers-go"
    awsconfig "github.com/aws/aws-sdk-go-v2/config"
    "github.com/aws/aws-sdk-go-v2/service/ecr"
)


func TestWithRedis(t *testing.T) {
    ctx := context.Background()
    cfg, _ := awsconfig.LoadDefaultConfig(ctx, awsconfig.WithRegion("eu-west1"))
    ecrClient := ecr.NewFromConfig(cfg)
    
    input := &ecr.GetAuthorizationTokenInput{}
    tokenResponse, _ := ecrClient.GetAuthorizationToken(ctx, input)
    
    token := *tokenresponse.AuthorizationData[0].AuthorizationToken
    decodedToken, _ := base64.StdEncoding.DecodeString(token)
    usernameAndPassword, _ := strings.SplitN(string(decodedToken), ":", 2)

    auth := types.AuthConfig{
        Username: usernameAndPassword[0]
        Password: usernameAndPassword[1]
    }

    authBytes, _ := json.Marshall(auth)
    base64Auth := base64.URLEncoding.EncodeToString(authBytes)

    req := testcontainers.ContainerRequest{
        Image:        "your-ecr-registry-address/redis:latest",
        ExposedPorts: []string{"6379/tcp"},
        WaitingFor:   wait.ForLog("Ready to accept connections"),
        RegistryCred: base64Auth
    }
    redisC, err := testcontainers.GenericContainer(ctx, testcontainers.GenericContainerRequest{
        ContainerRequest: req,
        Started:          true,
    })
    if err != nil {
        t.Error(err)
    }
    defer redisC.Terminate(ctx)
}

```
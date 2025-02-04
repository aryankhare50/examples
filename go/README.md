# rocket-car-gotest

## Recording test results

```sh
$ go get -d github.com/jstemmer/go-junit-report

$ go test -v ./... | go-junit-report > report.xml

$ BUILD_NAME=test
$ launchable record build --name ${BUILD_NAME}

| Name   | Path   | HEAD Commit                              |
|--------|--------|------------------------------------------|
| .      | .      | e4303d272680af330f96d5ff997ea116a3553cbb |


$ launchable record tests --build ${BUILD_NAME} go-test .

Launchable recorded tests for build test (test session 6) to workspace launchableinc/example from 1 files:

|   Files found |   Tests found |   Tests passed |   Tests failed |   Total duration (min) |
|---------------|---------------|----------------|----------------|------------------------|
|             1 |             6 |              6 |              0 |                 0.0833 |

Run `launchable inspect tests --test-session-id 6` to view uploaded test results


$ launchable inspect tests --test-session-id 6

| Test Path                                     |   Duration (sec) | Status   | Uploaded At                 |
|-----------------------------------------------|------------------|----------|-----------------------------|
| class=rocket-car-gotest#testcase=TestExample1 |                0 | SUCCESS  | 2021-12-20T05:53:04.961069Z |
| class=rocket-car-gotest#testcase=TestExample2 |                3 | SUCCESS  | 2021-12-20T05:53:04.961069Z |
| class=rocket-car-gotest#testcase=TestExample3 |                0 | SUCCESS  | 2021-12-20T05:53:04.961069Z |
| class=rocket-car-gotest#testcase=TestExample4 |                2 | SUCCESS  | 2021-12-20T05:53:04.961069Z |
| class=example#testcase=TestGreeting           |                0 | SUCCESS  | 2021-12-20T05:53:04.961069Z |
| class=example#testcase=ExampleGreeting        |                0 | SUCCESS  | 2021-12-20T05:53:04.961069Z |
```

---

## Subsetting your test runs

```sh
$ BUILD_NAME=test
$ CONFIDENCE="50%"

$ go test -list . ./... | \
    launchable subset \
    --build ${BUILD_NAME} \
    --confidence ${CONFIDENCE} \
    go-test > launchable-subset.txt
    
Your model is currently in training
Launchable created subset 6 for build test (test session 6) in workspace launchableinc/example

|           |   Candidates |   Estimated duration (%) |   Estimated duration (min) |
|-----------|--------------|--------------------------|----------------------------|
| Subset    |            1 |                  14.2857 |                1.66667e-05 |
| Remainder |            6 |                  85.7143 |                0.0001      |
|           |              |                          |                            |
| Total     |            7 |                 100      |                0.000116667 |

Run `launchable inspect subset --subset-id 6` to view full subset details


$ launchable inspect subset --subset-id 6

|   Order | Test Path                  | In Subset   |   Estimated duration (sec) |
|---------|----------------------------|-------------|----------------------------|
|       1 | testcase=TestExample3      | ✔           |                      0.001 |
|       2 | testcase=BenchmarkGreeting |             |                      0.001 |
|       3 | testcase=TestExample4      |             |                      0.001 |
|       4 | testcase=TestExample1      |             |                      0.001 |
|       5 | testcase=TestGreeting      |             |                      0.001 |
|       6 | testcase=TestExample2      |             |                      0.001 |
|       7 | testcase=ExampleGreeting   |             |                      0.001 |


$ go test -run $(cat launchable-subset.txt) ./... -v 2>&1 | \
    go-junit-report > report.xml

$ cat report.xml

<?xml version="1.0" encoding="UTF-8"?>
<testsuites>
    <testsuite tests="1" failures="0" time="0.000" name="github.com/launchableinc/rocket-car-gotest">
        <properties>
            <property name="go.version" value="go1.17.1"></property>
        </properties>
        <testcase classname="rocket-car-gotest" name="TestExample3" time="0.000"></testcase>
    </testsuite>
</testsuites>
```

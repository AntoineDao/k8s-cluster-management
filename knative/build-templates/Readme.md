# Build Templates
A build template is a series of steps to take code from a repository and deploy it to kubernetes cluster. Knative allows us to formulate relatively complex/similar builds that can be homogenously applied accross multiple repositories. [Here](https://github.com/knative/build/tree/master/test) is a series of examples of what can be achieved.

* [Environment Variable Injection](https://github.com/knative/build/tree/master/test/secret-env)
* [A ton of examples](https://github.com/knative/build/tree/master/test#running-unit-tests)
* [Source to url example](https://github.com/knative/docs/tree/master/serving/samples/source-to-url-go)
* [API Overview](https://github.com/knative/docs/blob/master/build/builds.md#steps)
* [Build Templates](https://github.com/knative/build-templates)
* [Buildpack Template](https://github.com/knative/build-templates/tree/master/buildpack) Seems good because straightforward and commonly used for Heroku. Will simplify developer lifecycle.
# Reproduce Map.Get pipeline performance

Use the configurable trace-pipeline benchmark in the companion
[benchmark proposal](https://github.com/githubnext/opentelemetry-collector-contrib/tree/oss/trace-pipeline-benchmark/benchmarks/tracebench).
Its public workload is synthetic, not a production calibration.

From that benchmark directory, with this branch fetched into a core checkout:

```sh
python3 compare.py --candidate map-get \
  --repository /path/to/opentelemetry-collector \
  --base 302c933caa8c66f7ebf35ea42238b261bc3e5fc6 \
  --head refs/remotes/githubnext/oss/pdata-map-get \
  --pgo --blocks 8 --iterations 256 --output /tmp/tracebench-map-get
```

The runner changes only Map.Get in a copy of the pinned dependency. Both
binaries use the same profile trained on the unchanged public baseline.
It retains CPU/allocated-byte observations, paired A/B and same-binary A/A
controls, module graphs and workload/stage/survival censuses.
Add `--spec /path/to/scenario.json` for an external scenario and choose whole
workload-bank iteration counts. No internal configurations are required.

Run `make test` and `make lint` in the pdata module as correctness checks.
The added `BenchmarkMapLookup` and `BenchmarkMapGetOrPutExisting` cover lookup
positions and dependent operations, but their ratios are not pipeline savings.
Do not assume a fixed percentage or a benefit on every workflow.

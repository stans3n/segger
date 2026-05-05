# segger reproduction log

Use one copy of this template per environment or workflow attempt. Record exact
versions from inside the container after a successful build.

## Run metadata

- Date:
- Operator:
- Git commit:
- Docker image tag:
- Host OS / WSL distro:

## NVIDIA driver and GPU

Paste `nvidia-smi` output:

```text

```

## Container package versions

Commands:

```bash
python --version
python -c "import torch; print(torch.__version__, torch.version.cuda)"
python -c "import cupy; print(cupy.__version__)"
python -c "import cudf, cuml, cugraph, cuspatial; print(cudf.__version__, cuml.__version__, cugraph.__version__, cuspatial.__version__)"
pip freeze
```

Record summary:

- Python:
- torch:
- torchvision:
- torch CUDA build:
- torch_scatter:
- torch_geometric:
- lightning:
- CuPy:
- cuDF:
- cuML:
- cuGraph:
- cuSpatial:

## Build command

```bash
docker build -f docker/Dockerfile.cuda121 -t segger:cuda121-local .
```

Result:

```text
SUCCESS on 2026-05-06 Australia/Sydney.
Image tag: segger:cuda121-local
Image id: sha256:003975b238b322255a240f76e8cef092c5a53094dacf50a3b30ddeb6454ccbd7
Docker build context: /home/anon1/projects/segger
Dockerfile: docker/Dockerfile.cuda121
```

Step 2 dependency strategy:

- Use Python 3.11 in the container virtual environment.
- Use CUDA 12.1 container base and PyTorch CUDA 12.1 wheels.
- Pin README-specified packages: `torch==2.5.0`, `torchvision==0.20.0`.
- Install `torch_scatter` from the `torch-2.5.0+cu121` wheel source.
- Pin RAPIDS CUDA 12 packages to the same 25.4.0 release series:
  `cuspatial-cu12==25.4.0`, `cudf-cu12==25.4.0`, `cuml-cu12==25.4.0`,
  `cugraph-cu12==25.4.0`.
- Leave CuPy, `torch_scatter`, and transitive dependencies unresolved by exact
  version until Step 3 is allowed to run the container and record the final
  installed versions.

Step 2 fixes before the successful build:

- Updated local image tag references from `segger:cuda121` to
  `segger:cuda121-local`.
- Pinned the RAPIDS CUDA 12 packages to one 25.4.0 release series.
- Replaced Dockerfile apt-list cleanup with `apt-get clean` so the Step 2 build
  did not rely on a host-visible dangerous cleanup command pattern.

Step 2 verification without running a container:

```bash
docker image inspect segger:cuda121-local
```

Result:

```text
SUCCESS.
RepoTags: segger:cuda121-local
WorkingDir: /workspace/segger
Cmd: bash
CUDA_VERSION: 12.1.1
Image size: 13401237516 bytes
```

## Run command

```bash
bash scripts/run_dev_container.sh
```

Result:

```text

```

## Verification commands and results

GPU check:

```bash
python scripts/check_gpu.py
```

Result:

```text

```

segger import and CLI check:

```bash
python scripts/check_segger_import.py
segger --help
```

Result:

```text

```

## Step 3/4 container and environment verification

Date: 2026-05-06 Australia/Sydney.
Git commit: ffbe813.
Docker image tag: `segger:cuda121-local`.
Container name: `segger-local-env`.

Step 3 container launch:

```bash
docker run -d --name segger-local-env --gpus all \
  -v "/home/anon1/projects/segger:/workspace/segger" \
  -v "/mnt/e/USYD/Honours Project/segger_rep/data:/data:ro" \
  -v "/mnt/e/USYD/Honours Project/segger_rep/outputs:/outputs" \
  -v "/mnt/e/USYD/Honours Project/segger_rep/work:/work" \
  -v "/mnt/e/USYD/Honours Project/segger_rep/logs:/logs" \
  -w /workspace/segger \
  segger:cuda121-local \
  sleep infinity
```

Step 3 result:

```text
SUCCESS.
Image: segger:cuda121-local
Privileged: false
GPU DeviceRequests: gpu, count all
Working directory: /workspace/segger
/workspace/segger: exists
/data: exists and is not writable inside the container
/outputs: exists and writable
/work: exists and writable
/logs: exists and writable
```

Step 4 result:

```text
nvidia-smi: SUCCESS, NVIDIA GeForce RTX 4070 visible
python --version: SUCCESS, Python 3.11.15
python -m pip freeze: SUCCESS, wrote /logs/pip-freeze-step4.txt
python scripts/check_gpu.py: SUCCESS, torch 2.5.0+cu121, CUDA available, GPU count 1
python scripts/check_segger_import.py: SUCCESS, segger and dependency imports passed
segger --help: SUCCESS, wrote /logs/segger-help-step4.txt
segger segment --help: SUCCESS, wrote /logs/segger-segment-help-step4.txt
```

Step 4 log files:

- `/mnt/e/USYD/Honours Project/segger_rep/logs/pip-freeze-step4.txt`
- `/mnt/e/USYD/Honours Project/segger_rep/logs/check-gpu-step4.txt`
- `/mnt/e/USYD/Honours Project/segger_rep/logs/check-segger-import-step4.txt`
- `/mnt/e/USYD/Honours Project/segger_rep/logs/segger-help-step4.txt`
- `/mnt/e/USYD/Honours Project/segger_rep/logs/segger-segment-help-step4.txt`

No real segmentation was run. No writes were made to `/data`. No packages were
installed into the WSL or Windows system Python.

## Errors and fixes

| Date | Command | Error | Fix attempted | Result |
| --- | --- | --- | --- | --- |
| | | | | |

## Reproduction status

- Environment reproduction completed: yes
- Workflow reproduction completed: no
- Scientific result reproduction completed: no

Notes:

```text

```

## Step 5 Xenium Breast 2-FOV smoke test

Date: 2026-05-06 Australia/Sydney.
Container name: `segger-local-env`.
Docker image tag: `segger:cuda121-local`.
Input directory: `/data/xenium`.
Output directory: `/outputs/step5-xenium-breast-2fov-smoke`.
Log file: `/logs/step5-xenium-breast-2fov-smoke.log`.

Actual command:

```bash
segger segment -i /data/xenium -o /outputs/step5-xenium-breast-2fov-smoke --n-epochs 1 --max-nodes-per-tile 10000 --max-edges-per-batch 200000 --node-representation-dim 64 --hidden-channels 32 --out-channels 32 --n-mid-layers 1 --transcripts-max-k 3 --prediction-max-k 3
```

Timing:

```text
Start time: 2026-05-05T16:20:24+00:00
End time: 2026-05-05T16:21:38+00:00
Exit code: 139
```

Output files:

```text
/outputs/step5-xenium-breast-2fov-smoke
```

Expected files:

```text
segger_segmentation.parquet: not generated
segger_anndata.h5ad: not generated
Lightning logs: none found
```

Step 5 result:

```text
FAILED.
Failure stage: graph construction.
Evidence: segger emitted "Some cells have zero counts", then crashed with signal
11 in libucx/libcuda at cuCtxGetDevice_v2 before producing segmentation,
AnnData output, or Lightning logs.
Minimal next fixes to consider, not executed: test a lower graph workload
(`--max-nodes-per-tile` and `--max-edges-per-batch`), or isolate whether the
CUDA/UCX crash is triggered by graph construction on WSL/GPU for this dataset.
No writes were made to /data.
```

## Step 5b failure diagnosis and minimal repair experiment

Date: 2026-05-06 Australia/Sydney.
Container name: `segger-local-env`.
Docker image tag: `segger:cuda121-local`.
Input directory: `/data/xenium`.

Step 5 evidence review:

```text
Original Step 5 result: FAILED.
Original exit code: 139.
Original log: /logs/step5-xenium-breast-2fov-smoke.log.
Last 200 log lines still show signal 11 in libucx / WSL libcuda at
cuCtxGetDevice_v2 after "Some cells have zero counts".
dmesg tail inside the container produced no OOM/kernel evidence.
```

CUDA / UCX / RAPIDS diagnostics:

```text
Command log: /logs/step5b-cuda-ucx-diagnostics.log
Result: PASSED.
nvidia-smi: visible NVIDIA GeForce RTX 4070.
torch: 2.5.0+cu121, CUDA available, CUDA 12.1.
cupy: 14.0.1, simple GPU array sum returned 45.
cudf: 25.04.00 import ok.
cuml: 25.04.00 import ok.
cugraph: 25.04.00 import ok.
cuspatial: 25.04.00 import ok.
CUDA_VISIBLE_DEVICES: unset.
UCX_TLS: unset.
RAPIDS_NO_INITIALIZE: unset.
```

Input boundary diagnostics:

```text
Command log: /logs/step5b-input-summary.log
Result: FAILED.
/data/xenium exists: true.
transcripts.parquet: exists, 20,923,989 bytes, 1,113,950 rows.
cell_boundaries.parquet: exists, 700,126 bytes.
nucleus_boundaries.parquet: exists, 657,067 bytes.
transcripts.parquet schema was read successfully with polars.
geopandas.read_parquet(cell_boundaries.parquet) failed:
ValueError: Missing geo metadata in Parquet/Feather file.
Use pandas.read_parquet/read_feather() instead.
```

Minimal retry:

```text
Command: not run.
Reason: Step 5b instructions said to stop and not run segment if the input
boundary diagnostic failed.
Output directory checked: /outputs/step5b-xenium-breast-2fov-minimal
segger_segmentation.parquet: not generated.
segger_anndata.h5ad: not generated.
```

Step 5b result:

```text
FAILED.
Failure stage: data recognition / preprocessing boundary.
Current evidence does not show a general CUDA/RAPIDS import failure. The
explicit input diagnostic failed because the boundary parquet files are not
GeoParquet files readable by geopandas.read_parquet. The original segger crash
may still involve CUDA/UCX during later graph construction, but Step 5b stopped
before the minimal retry because the requested input boundary check failed.
Minimal next fixes to consider, not executed: verify the expected Xenium
boundary parquet format for this segger CLI path, inspect whether segger expects
plain parquet plus geometry construction instead of GeoParquet metadata, or run
a narrower pandas/pyarrow boundary schema check before retrying segment.
No writes were made to /data.
```

## Step 5c Xenium boundary schema and reader path diagnosis

Date: 2026-05-06 Australia/Sydney.
Container name: `segger-local-env`.
Docker image tag: `segger:cuda121-local`.
Input directory: `/data/xenium`.
Schema diagnostic log: `/logs/step5c-boundary-schema.log`.
Reader path snippets: `docs/reproduction-log/step5c-reader-path-snippets.txt`.

Boundary schema diagnostics:

```text
Result: PASSED with pyarrow and pandas.
cell_boundaries.parquet: 181,804 rows, columns cell_id, vertex_x, vertex_y,
label_id, parquet metadata keys ['pandas'].
nucleus_boundaries.parquet: 181,302 rows, columns cell_id, vertex_x, vertex_y,
label_id, parquet metadata keys ['pandas'].
cells.parquet: 7,275 rows.
transcripts.parquet: 1,113,950 rows.
Boundary coordinate ranges are within the transcript coordinate ranges.
```

Interpretation of Step 5b geopandas error:

```text
geopandas.read_parquet failed because the 10x Xenium boundary parquet files are
ordinary parquet vertex tables with pandas metadata, not GeoParquet files. This
does not by itself indicate corrupt input data.
```

SEGGER boundary reader path:

```text
src/segger/io/preprocessor.py XeniumPreprocessor._get_boundaries reads raw
cell_boundaries.parquet and nucleus_boundaries.parquet using pl.read_parquet,
then converts vertex_x / vertex_y grouped by cell_id through
contours_to_polygons.
The gpd.read_parquet calls in ISTPreprocessor.preprocess load generated cached
cell_boundaries_geo.parquet and nucleus_boundaries_geo.parquet outputs, not the
raw 10x boundary parquet files.
```

Step 5c conclusion:

```text
Situation A.
Boundary parquet input is likely normal for 10x Xenium and readable by the
SEGGER Xenium reader path. The Step 5b geopandas failure was a diagnostic-method
artifact, not evidence that the input parquet is bad. The original exit 139 is
still more likely a graph construction / CUDA / UCX native crash after input
preprocessing begins.
Next suggested experiment, not executed in Step 5c: Step 5d minimal retry with
runtime-only environment variables UCX_MEMTYPE_CACHE=n,
UCX_TLS=tcp,self,cuda_copy, and OMP_NUM_THREADS=1.
No writes were made to /data, and segger segment was not run in Step 5c.
```

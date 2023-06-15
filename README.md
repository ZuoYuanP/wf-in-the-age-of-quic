# Website Fingerprinting in the Age of QUIC

代码来自论文 "Website Fingerprinting in the Age of QUIC" (PETS 2021).

代码被分成workflows,一个workflows负责一个或几个相关实验，
每个workflows都包含用于收集和处理数据、执行机器学习分类以及为论文生成图表的脚本。

## Data Availability Statements

The data used to support this paper are provided in two sets:

- [**quic-wf-core.tgz (831 MB)**](https://polybox.ethz.ch/index.php/s/u10mAN6NCcDP39U):
  - 用于扫描的域名和以CSV格式提供的带有headers的扫描结果；
  - 具有class、protocol和 VPN 位置标签的 HDF5 格式的数据集; size和timestamp数组；低于175字节的数据包被移除；
- **quic-wf-raw.tar (28 GB)**:
  - 原始获取的QUIC和TCP跟踪及其关联的元数据.
  - 每个文件都是一个JSON对象流，包含以下可能为空值的键:
    - *url, final_url*: requested and final redirected URLs
    - *status*: HTTP status code of the fetch
    - *protocol*: protocol used to request the main page, "quic" or "tcp"
    - *packets*: base64 encoded PCAP for the request
    - *http_trace*: Chromium DevTools performance log ([reference](https://chromedevtools.github.io/devtools-protocol/tot/Network/))
  - 由于大小限制，此内容仅根据请求提供。.
  - **New 2022-01-24**: Also available in [**pcapml**](https://nprint.github.io/pcapml.html) format upon request!

> **Note:** 数据集中的QUIC trace是指一种包含了QUIC和TCP数据包的Wireguard隧道的跟踪，这些数据包与通过QUIC连接请求网页相关。这意味着至少与Web服务器建立的初始连接是通过QUIC完成的

## Computational Requirements

### Software Requirements

- Bash
- Git and Git-LFS 2.17
- Python 3.7
  - Dependencies listed in `requirements.txt`
  - The [**wf-tools**](https://github.com/jpcsmith/wf-tools) library
- Optional:
  - Docker 19.03.12
  - Wireguard v1.0.20200513
  - docker-machine v0.16.2

### Memory and Runtime Requirements

该代码最后在一个计算集群上运行，每个实验在2-4个核心上运行（每个核心2.4 GHz），每个核心配备6 GB主内存。
机器学习的训练和测试使用了额外的0-2个基于分类器的GPU。
为了减少运行时间，这些作业是并行运行的，每个训练-测试拆分需要1-3小时。

**Note:** 当前的 requirements.txt 文件指定了使用 tensorflow-cpu。如果你有 GPU 可以使用的话，请安装 tensorflow-gpu 替代.

## Getting Started

一个包含已下载代码和数据的虚拟机镜像当前可通过此 [**link**](https://polybox.ethz.ch/index.php/s/YGYX4qNu9hxUpvn)快速访问.

下说明描述了如何从头开始设置和运行workflows.

#### 1. Clone the repository and change to the directory.

```bash
# Clone the repository
git clone https://github.com/jpcsmith/wf-in-the-age-of-quic.git
# Change to the code directory
cd wf-in-the-age-of-quic/
# Download the git LFS files
git lfs pull
```

#### 2. Create and activate a virtual environment

```bash
python3 -m venv env
source env/bin/activate
```

#### 3. Install the Python requirements
```bash
# Ensure that pip is the latest version
python3 -m pip install --upgrade pip

# Install the requirements using pip
python3 -m pip install --no-cache-dir -r requirements.txt
```

If the installation fails, ensure that the Python development libraries are installed and retry the above.
On Ubuntu 18.04, this would be the `python3.7-dev` and `python3-venv` packages.

#### 4. (Optional) Download and extract the data

```bash
wget https://polybox.ethz.ch/index.php/s/u10mAN6NCcDP39U/download -O quic-wf-core.tgz
tar -xzvf quic-wf-core.tgz
```

#### 5. (Optional) Install Docker and Wireguard
If planning to run trace-collection, i.e. from the [Fetch QUIC Traces] workflow, install docker (19.03.12) and Wireguard (v1.0.20200513).

#### 6. Run the desired workflow
Change to the desired workflow's directory and follow the instructions for running the workflow.

## workflows和论文的对应章节

| Paper Section | Workflows | Directories |
|-------------------------------|----------|-----------|
| 4. Combined QUIC-TCP Dataset  | Identify QUIC Sites<br>Fetch QUIC Traces | [**workflows/identify-quic-sites**](workflows/identify-quic-sites)<br>[**workflows/fetch-any-quic**](workflows/fetch-any-quic) |
| 6. From TCP to QUIC           | Generalisability Analysis<br>Single and Mixed Analyses | [**workflows/generalisability-analysis**](workflows/generalisability-analysis)<br>[**workflows/single-and-mixed-analyses**](workflows/single-and-mixed-analyses) |
| 7. Joint Classification of QUIC and TCP | Single and Mixed Analyses<br>Distinguish Protocol | [**workflows/single-and-mixed-analyses**](workflows/single-and-mixed-analyses)<br>[**workflows/distinguish-protocol**](workflows/distinguish-protocol) |
| 8. Remove Control Packets | Removing Control Packets | [**workflows/removing-control-packets**](workflows/removing-control-packets) |


## List of Tables and Programs

下表列出了负责论文中各个表格和图形的程序和文件。
Notebooks位于notebooks/目录中，输出文件位于results/plots目录中 **relative to the associated workflow**.

| Figure/Table   | Workflow                    | Notebook                                 | Output file                            |
|----------------|-----------------------------|------------------------------------------|----------------------------------------|
| Table 2        | [Generalisability Analysis] | [**confusion-matrix.ipynb**]             | `confusion-matrix.tex`                 |
| Figure 2       | [Generalisability Analysis] | [**result-analysis.ipynb**]              | `score-vs-quic-presence.pgf`           |
| Figure 3       | [Single and Mixed Analyses] | [**feature-analysis.ipynb**]             | `feature-rank-comparison.pgf`          |
| Figure 4       | [Single and Mixed Analyses] | [**determine-num-quic-features.ipynb**]  | `quic-feature-scores.pgf`              |
| Figure 5       | [Distinguish Protocol]      | [**split-classify.ipynb**]               | `split-classify.pgf`                   |
| Figure 6       | [Distinguish Protocol]      | [**distinguisher-performance.ipynb**]    | `distinguisher-performance.pgf`        |
| Figure 7       | [Distinguish Protocol]      | [**distinguisher-performance.ipynb**]    | `distinguisher-importance.pgf`         |
| Figure 8       | [Removing Control Packets]  | [**min-size-analysis.ipynb**]            | `packet-size-ecdf.pgf`                 |
| Table 3        | [Removing Control Packets]  | [**min-size-analysis.ipynb**]            | embedded in notebook                   |
| Figure 9       | [Fetch QUIC Traces]         | [**resource-distribution.ipynb**]        | `quic-resource-dist.pgf`               |
| Figure 10      | [Generalisability Analysis] | [**vary-deploy.ipynb**]                  | `vary-deployment.pgf`                  |
| Figure 11a     | [Generalisability Analysis] | [**result-analysis-curve.ipynb**]        | `quic-presence-prcurve.pgf`            |
| Figure 11b     | [Distinguish Protocol]      | [**split-classify.ipynb**]               | `split-classify-prcurve.pgf`           |
| Figure 11c     | [Removing Control Packets]  | [**min-size-analysis-curve.ipynb**]      | `score-vs-min-packet-size-prcurve.pgf` |


[Generalisability Analysis]: ./workflows/generalisability-analysis
[Single and Mixed Analyses]: ./workflow/single-and-mixed-analyses
[Distinguish Protocol]: ./workflows/distinguish-protocol
[Removing Control Packets]: ./workflows/removing-control-packets
[Fetch QUIC Traces]: ./workflows/fetch-any-quic

[**confusion-matrix.ipynb**]: workflows/generalisability-analysis/notebooks/confusion-matrix.ipynb
[**result-analysis.ipynb**]: workflows/generalisability-analysis/notebooks/result-analysis.ipynb
[**feature-analysis.ipynb**]: workflows/single-and-mixed-analyses/notebooks/feature-analysis.ipynb
[**determine-num-quic-features.ipynb**]: workflows/single-and-mixed-analyses/notebooks/determine-num-quic-features.ipynb
[**split-classify.ipynb**]: workflows/distinguish-protocol/notebooks/split-classify.ipynb
[**distinguisher-performance.ipynb**]: workflows/distinguish-protocol/notebooks/distinguisher-performance.ipynb
[**min-size-analysis.ipynb**]: workflows/removing-control-packets/notebooks/min-size-analysis.ipynb
[**resource-distribution.ipynb**]: workflows/fetch-any-quic/notebooks/resource-distribution.ipynb
[**vary-deploy.ipynb**]: workflows/generalisability-analysis/notebooks/vary-deploy.ipynb
[**result-analysis-curve.ipynb**]: workflows/generalisability-analysis/notebooks/result-analysis-curve.ipynb
[**min-size-analysis-curve.ipynb**]: workflows/removing-control-packets/notebooks/min-size-analysis-curve.ipynb

## Licence

The code and associated data is released under an MIT licence as found in the [LICENCE](./LICENCE) file.

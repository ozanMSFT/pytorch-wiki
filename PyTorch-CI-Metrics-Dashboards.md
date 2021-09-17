# PyTorch CI Metrics/Dashboards Compilation

This page is a compilation of dashboards we have for tracking PyTorch CI, including test time, test status, and when the docs were last updated.

## OSS:

* Grafana: https://metrics.pytorch.org/?orgId=1
* The HUD
    * https://hud.pytorch.org/build2/pytorch-master cleaner
    * https://hud.pytorch.org/build1/pytorch-master has more data, like estimated cost per commit
* PyTorch download stats

## Internal Dashboards

* Some of the following should be combined into one folder structure.
* Michael’s trunk health [Unidash](https://fburl.com/unidash/90352rv8)
* Rong’s [potentially outdated] Collation of [Unidash](https://www.internalfb.com/intern/unidash/dashboard/pytorch_oss_ci/), containing
    * Binary build TTS
    * Test TTS
    * Test Status
* PyTorch Dev Infra Perf [collation](https://www.internalfb.com/intern/unidash/dashboard/pytorch_dev_infra_performance) (a few of these are broken) 
    * OSS CI Test Time Details
    * OSS CI Build Time Details
    * Landhugger
    * Internal Statuses
* Ed’s [potentially outdated] PyTorch OSS [Unidash](https://www.internalfb.com/intern/unidash/dashboard/pytorch_oss_metrics/main)
    * Has stats based on branch: master, nightly, postnightly, pull request
* viable/strict promo [logs](https://www.internalfb.com/intern/chronos/job?smc=chronos_gp_admin_client&jobname=OpensourcePushViableBranchScript)
* Links to nightly cron jobs per domain
    * [nightlies trigger](https://www.internalfb.com/intern/chronos/job?smc=chronos_gp_admin_client&jobname=nightly_trigger)
    * [torchaudio](https://www.internalfb.com/intern/chronos/job/?jobname=torchaudio%20nightly%20trigger&smc=chronos_gp_admin_client)
    * [torchvision](https://www.internalfb.com/intern/chronos/job/?jobname=torchvision%20nightly%20trigger&smc=chronos_gp_admin_client)
    * [torchtext](https://www.internalfb.com/intern/chronos/job/?jobname=torchtext%20nightly%20trigger&smc=chronos_gp_admin_client)
* TorchBench [stats](https://fburl.com/scuba/pytorch_benchmarks/gkg93dcr)
* Internal stats from other teams
    * Fortify Reliability (https://fburl.com/unidash/kicivh9f) (from internal DevX)
    * Remote worker status (https://fburl.com/unidash/iu7kdxza) (from internal DevX, mostly for GPU and mobile RE)


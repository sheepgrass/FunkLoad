# Makefile for simple demo
MONCTL := fl-monitor-ctl monitor.conf

ifdef URL
# FunkLoad options
	FLOPS = -u $(URL) $(EXT)
else
	FLOPS = $(EXT)
endif

all: test

# testing
test:
	fl-run-test test_Simple.py -v $(FLOPS)

# benching for 1 concurrent users
bench_1:
	-rm -rf log-distributed
	-rm -rf *.xml*
	-rm -rf report-CU1
	-rm -rf report_CU1.tar.gz
	@$(MONCTL) restart
	-fl-run-bench -c 1 --simple-fetch --label=CU1 test_Simple.py Simple.test_simple --distribute $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report log-distributed/*.xml --html --quiet -r report-CU1
	-tar -zcvf report-CU1.tar.gz report-CU1

# benching for 1, 10, 20 concurrent users
bench_1_10_20:
	-rm -rf log-distributed
	-rm -rf *.xml*
	-rm -rf report-CU1_10_20
	-rm -rf report-CU1_10_20.tar.gz
	@$(MONCTL) restart
	-fl-run-bench -c 1:10:20 --simple-fetch --label=CU1_10_20 test_Simple.py Simple.test_simple --distribute $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report log-distributed/*.xml --html --quiet -r report-CU1_10_20
	-tar -zcvf report-CU1_10_20.tar.gz report-CU1_10_20

# benching for 50, 100 concurrent users
bench_50_100:
	-rm -rf log-distributed
	-rm -rf *.xml*
	-rm -rf report-CU50_100
	-rm -rf report-CU50_100.tar.gz
	@$(MONCTL) restart
	-fl-run-bench -c 50:100 --simple-fetch --label=CU50_100 test_Simple.py Simple.test_simple --distribute $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report log-distributed/*.xml --html --quiet -r report-CU50_100
	-tar -zcvf report-CU50_100.tar.gz report-CU50_100

# benching for 500 concurrent users
bench_500:
	-rm -rf log-distributed
	-rm -rf *.xml*
	-rm -rf report-CU500
	-rm -rf report-CU500.tar.gz
	@$(MONCTL) restart
	-fl-run-bench -c 500 --simple-fetch --label=CU500 test_Simple.py Simple.test_simple --distribute $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report log-distributed/*.xml --html --quiet -r report-CU500
	-tar -zcvf report-CU500.tar.gz report-CU500

# benching for 1000 concurrent users
bench_1000:
	-rm -rf log-distributed
	-rm -rf *.xml*
	-rm -rf report-CU1000
	-rm -rf report-CU1000.tar.gz
	@$(MONCTL) restart
	-fl-run-bench -c 1000 --simple-fetch --label=CU1000 test_Simple.py Simple.test_simple --distribute $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report log-distributed/*.xml --html --quiet -r report-CU1000
	-tar -zcvf report-CU1000.tar.gz report-CU1000

# benching
bench: bench_fast

bench_all: bench_fast bench_fast_multicpu bench_resources bench_sleep diff_reports

# fastest with cpu affinity
bench_fast:
	@$(MONCTL) restart
	-taskset -c 0 fl-run-bench -c 1:2:3:4:5:6:8:10:16 -D 4 -f --simple-fetch --label=onecpu test_Simple.py Simple.test_simple $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report simple-bench.xml --html -r report-onecpu

# fast on all available cpu
bench_fast_multicpu:
	@$(MONCTL) restart
	-fl-run-bench -c 1:2:3:4:5:6:8:10:16 -D 4 -f --simple-fetch --label=allcpu test_Simple.py Simple.test_simple $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report simple-bench.xml --html -r report-allcpu

# fast cpu affinity and fetching resources
bench_resources:
	@$(MONCTL) restart
	-taskset -c 0 fl-run-bench -c 1:2:3:4:5:6:8:10:16 -D 4 -f --label=resources test_Simple.py Simple.test_simple $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report simple-bench.xml --html -r report-resources


# sleep 0.5ms between requests
bench_sleep:
	@$(MONCTL) restart
	-taskset -c 0 fl-run-bench -c 1:2:3:4:5:6:8:10:16 -D 4 -m0 -M0.001 --simple-fetch --label=sleep test_Simple.py Simple.test_simple $(FLOPS)
	@$(MONCTL) stop
	-fl-build-report simple-bench.xml --html -r report-sleep


diff_reports:
	-fl-build-report --diff report-allcpu report-onecpu -r diff_one_vs_multi_cpu
	-fl-build-report --diff report-resources report-onecpu -r diff_nofetch_vs_fetchresources


# monitor ctl
start_monitor:
	$(MONCTL) start

stop_monitor:
	-$(MONCTL) stop

restart_monitor:
	$(MONCTL) restart


clean:
	-find . "(" -name "*~" -or  -name ".#*" ")" -print0 | xargs -0 rm -f

# Core Simulation 01 – Data does not appear in the index

**🔹 Title:** Data does not appear in the index after input configuration

**❗ Problem:**
After configuring a log monitoring via `inputs.conf`, the data does not appear in the expected index (`index=main`).

**🧪 Simulated Cause:**
`inputs.conf` is pointing to an index that does not exist (`index=fakeindex`).

**🔍 Investigation Steps:**
1. Check `inputs.conf` with `btool`:
```bash
./splunk btool inputs list --debug | grep -A 5 "[monitor"
```
2. Validate if the `fakeindex` index exists:
```spl
| eventcount summarize=false index=fakeindex
```
3. Check internal logs:
```spl
index=_internal sourcetype=splunkd component=MetricsProcessor
```
4. Check if the forwarder is sending data correctly:
```bash
./splunk list forward-server
./splunk display listen
```

**🔧 Fix:**
Edit `inputs.conf` to use a valid index (e.g. `index=main`) and restart Splunk:
```bash
vim $SPLUNK_HOME/etc/system/local/inputs.conf
# Fix: index=main
./splunk restart
```

**✅ Expected Result:**
After the fix, the data starts to appear normally in `index=main`.

**💡 Lesson Learned:**
Simple mistakes like an incorrect index name can cause a complete lack of visible data. Using `btool` and `_internal` greatly speeds up diagnostics.

Splunk Doc:

https://docs.splunk.com/Documentation/Splunk/latest/SearchReference/Eventcount
https://docs.splunk.com/Documentation/Splunk/latest/Troubleshooting/Usebtooltotroubleshootconfigurations

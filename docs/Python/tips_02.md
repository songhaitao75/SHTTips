# pytest & allure
### 强制更改allure的结果 broken -> failed

- allure_pytest 中，会将除AssertionError 和pytest.fail.Exception的其他Error情况的testcase结果标为BROKEN
```python
### Python\Python36\Lib\site-packages\allure_pytest\utils.py
def get_status(exception):
    if exception:
        if isinstance(exception, AssertionError) or isinstance(exception, pytest.fail.Exception):
            return Status.FAILED
        elif isinstance(exception, pytest.skip.Exception):
            return Status.SKIPPED
        return Status.BROKEN
    else:
        return Status.PASSED
```

```python
# Python\Python36\Lib\site-packages\allure_pytest\plugin.py
    @pytest.hookimpl(hookwrapper=True)
    def pytest_runtest_makereport(self, item, call):
        uuid = self._cache.get(item.nodeid)

        report = (yield).get_result()

        test_result = self.allure_logger.get_test(uuid)
        status = get_pytest_report_status(report)
        status_details = None

        if call.excinfo:
            message = escape_non_unicode_symbols(call.excinfo.exconly())
            if hasattr(report, 'wasxfail'):
                reason = report.wasxfail
                message = ('XFAIL {}'.format(reason) if reason else 'XFAIL') + '\n\n' + message
            trace = escape_non_unicode_symbols(report.longreprtext)
            status_details = StatusDetails(
                message=message,
                trace=trace)
            if (status != Status.SKIPPED
                    and not (call.excinfo.errisinstance(AssertionError)
                             or call.excinfo.errisinstance(pytest.fail.Exception))):
                status = Status.BROKEN

        if status == Status.PASSED and hasattr(report, 'wasxfail'):
            reason = report.wasxfail
            message = 'XPASS {reason}'.format(reason=reason) if reason else 'XPASS'
            status_details = StatusDetails(message=message)

        if report.when == 'setup':
            test_result.status = status
            test_result.statusDetails = status_details

        if report.when == 'call':
            if test_result.status == Status.PASSED:
                test_result.status = status
                test_result.statusDetails = status_details

        if report.when == 'teardown':
            if status in (Status.FAILED, Status.BROKEN) and test_result.status == Status.PASSED:
                test_result.status = status
                test_result.statusDetails = status_details

            if self.config.option.attach_capture:
                if report.caplog:
                    self.attach_data(report.caplog, "log", AttachmentType.TEXT, None)
                if report.capstdout:
                    self.attach_data(report.capstdout, "stdout", AttachmentType.TEXT, None)
                if report.capstderr:
                    self.attach_data(report.capstderr, "stderr", AttachmentType.TEXT, None)

```

如果想避免此更改，可在自己的项目中，调用pytest的hooks function，检查case执行抛出的Error类型，并进行强制更改。例如selenium中的StaleElementReferenceException
```python
# conftest.py
def pytest_runtest_makereport(call):
    if call.excinfo:
        from selenium.common.exceptions import StaleElementReferenceException
        if call.excinfo.errisinstance(StaleElementReferenceException):
            logger.info(f"Forcibly replace ‘StaleElementReferenceException’ with ‘AssertionError’"
                        f" to avoid allure marking the test as 'broken'")
            origin_excinfo = call.excinfo._excinfo
            trackback_entries = []
            for trackback_entry in call.excinfo.traceback:
                project_path = os.path.abspath(os.path.dirname(__file__))
                if trackback_entry.path.strpath.startswith(project_path):
                    trackback_entries.append(trackback_entry)
            trackback_entries_messages = ["\t" + entry.__str__() for entry in trackback_entries]
            error_messages = [
                "Page element disappears abnormally",
                "Caught Error:",
                "\t" + origin_excinfo[1].__repr__(),
                "Trackback Message:",
            ]
            error_messages.extend(trackback_entries_messages)
            error_message = "\n".join(error_messages)
            refactor_excinfo = (AssertionError, AssertionError(error_message), origin_excinfo[2])
            call.excinfo._excinfo = refactor_excinfo
```

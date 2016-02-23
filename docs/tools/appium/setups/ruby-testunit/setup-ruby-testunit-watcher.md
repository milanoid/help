---
title: Watcher Setup on TestObject using Test::Unit
layout: en
permalink: docs/tools/appium/setups/testunit/watcher/
---

If you want to register your test result on TestObject, you will need to use our result watcher. This is what a simple Watcher setup with Test::Unit looks like.

To begin testing on our platform with Ruby, you need to install some dependencies. First, you must install the Ruby library supporting Appium and also Test::Unit, a testing framework for Ruby. This setup also needs our TestObject Test Watcher for Ruby. Install the dependencies by running:
{% highlight bash %}
gem install appium_lib
gem install test-unit
gem install test_object_test_result_watcher
{% endhighlight %}

With the dependencies installed, you can begin testing on TestObject. The following setup allows you to test and register your results on TestObject:

{% highlight ruby %}
require 'appium_lib'
require 'test/unit'
require 'test_object_test_result_watcher'

class BasicTestSetup < Test::Unit::TestCase
  def setup
    desired_capabilities = {
        caps:       {
            testobject_api_key: 'YOUR_API_KEY',
            testobject_app_id: '1',
            testobject_device: 'Motorola_Moto_G_2nd_gen_real',
            testobject_report_results: true
        },
        appium_lib: {
            server_url: 'https://app.testobject.com:443/api/appium/wd/hub',
            wait: 10
        }
    }

    # Start the driver
    @driver = Appium::Driver.new(desired_capabilities).start_driver
    @testwatcher = TestObjectTestResultWatcher.new(desired_capabilities, @driver)
  end

  # test method names must start with "test_"
  # to be recognized by the test-unit framework
  def test_login
    # Your test
  end

  def teardown
    @testwatcher.report_results_and_cleanup(passed?)
  end
end
{% endhighlight %}

Along with the mandatory capabilities we have specified, you can send over some optional ones to customize your test runs:

* testobject_appium_version, if you want to run your tests against an Appium version other than the default one (1.3.5)
* testobject_test_name, if you want to specify a name for your test that will be displayed in place of the default one
* testobject_suite_name, if you want to apply a label to your tests so that they are easier to group / find later on
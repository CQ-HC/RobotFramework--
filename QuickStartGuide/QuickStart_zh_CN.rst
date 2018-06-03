...

Demo application
----------------

本指南的示例应用程序是典型登录示例的变体: 它是一个以 Python 编写的基于命令行的身份验证服务器。该应用程序允许用户做以下3件事:

--创建具有有效密码的帐户。
--使用有效的用户名和密码登录。
--更改现有帐户的密码。

应用程序本身位于 sut/login.py 文件中, 并且可以使用指令 python sut/login.py 执行。尝试使用不存在的用户帐户登录或使用无效密码会导致同一错误信息::

	> python sut/login.py login nobody P4ssw0rd
    Access Denied

创建一个具有有效密码的用户帐户后, 登录成功::

	> python sut/login.py create fred P4ssw0rd
    SUCCESS

    > python sut/login.py login fred P4ssw0rd
    Logged In

密码必须满足两个要求才能有效: 它必须介于7-12 个字符之间, 并且它必须包含小写和大写字母和数字, 但不能包含特殊字符。尝试创建无效密码的用户失败::

	> python sut/login.py create fred short
    Creating user failed: Password must be 7-12 characters long

	> python sut/login.py create fred invalid
    Creating user failed: Password must be a combination of lowercase and
    uppercase letters and numbers


使用无效凭据更改密码会导致与使用无效凭据登录相同的错误消息。验证了新密码的有效性, 如果无效, 则给出一条错误消息:

	> python sut/login.py change-password fred wrong NewP4ss
    Changing password failed: Access Denied

    > python sut/login.py change-password fred P4ssw0rd short
    Changing password failed: Password must be 7-12 characters long

    > python sut/login.py change-password fred P4ssw0rd NewP4ss
    SUCCESS


应用程序使用一个简单的数据库文件来跟踪用户状态。
该文件位于与操作系统相关的临时目录中。


Executing this guide
====================

这些指南说明如何自己运行本指南。如果您对此不感兴趣, 则可以在线查看结果。

__ `Viewing results`_

Installations
-------------

在 Python_ 上安装Robot Framework的推荐方法是使用`pip <http://pip-installer.org>`_.一旦安装了这两个先决条件, 就可以简单地运行::

	pip install robotframework

请参阅 "机器人框架安装说明(Robot Framework installation instructions)" 可供选择的安装方法和有关安装的更多信息。

此演示是使用 reStructuredText__ 标记语言编写的, 它具有代码块中的Robot Framework测试数据。执行此格式的测试需要安装其他 docutils__ 模块::

	pip install docutils

请注意, Robot Framework 3.0 是第一个支持 Python 3 的Robot Framework版本。有关 python 2 vs python 3 的信息, 请参阅上述的 "安装说明(installation instructions)"。

.. _`Robot Framework installation instructions`:
   https://github.com/robotframework/robotframework/blob/master/INSTALL.rst
.. _`installation instructions`: `Robot Framework installation instructions`_
__ http://docutils.sourceforge.net/rst.html
__ https://pypi.python.org/pypi/docutils


Execution
---------

安装后, 您仍然需要获得demo本身。下载某个 release__ 或抓取 "最新内容" 并在某个地方提取软件包是最容易的, 但也可以克隆 "项目存储库"。

安装后和所有其他先决条件都到位后, 您可以使用 "robot" 命令在命令行上运行演示::

	robot QuickStart.rst

如果你使用Robot Framework 2.9 或更旧, 而不是 ' robot ' 命令你需要使用 ' pybot '::
	
	pybot QuickStart.rst

还可以使用各种命令行选项配置执行::

	robot --log custom_log.html --name Custom_Name QuickStart.rst

得到可用选项的列表,运行 "robot --help"。

__ https://github.com/robotframework/QuickStartGuide/releases
__ https://github.com/robotframework/QuickStartGuide/archive/master.zip
__ https://github.com/robotframework/QuickStartGuide


Viewing results
---------------

运行demo会生成以下三个结果文件。这些文件链接到在网上可用的预执行文件, 但执行demo会在本地创建它们。

`report.html <http://robotframework.org/QuickStartGuide/report.html>`__
   高级测试报告。(Higher level test report)
`log.html <http://robotframework.org/QuickStartGuide/log.html>`__
   详细的测试执行日志 (Detailed test execution log)
`output.xml <http://robotframework.org/QuickStartGuide/output.xml>`__
   计算机可读 XML 格式的结果 (Results in machine readable XML format)



Test cases
==========

Workflow tests
--------------
Robot Framework测试用例是使用简单的表格语法创建的。例如, 下表有两个测试:

--用户可以创建帐户并登录
--用户无法使用错误的密码登录

.. code:: robotframework

    *** Test Cases ***
    User can create an account and log in
        Create Valid User    fred    P4ssw0rd
        Attempt to Login with Credentials    fred    P4ssw0rd
        Status Should Be    Logged In

    User cannot log in with bad password
        Create Valid User    betty    P4ssw0rd
        Attempt to Login with Credentials    betty    wrong
        Status Should Be    Access Denied

请注意, 这些测试读起来像是用英语编写的手动测试, 而不是像自动测试用例。机器人框架使用关键字驱动的方法, 支持编写测试, 捕捉自然语言中的动作和期望的本质。

测试用例是通过关键字及其可能的参数构造的。该语法要求关键字和参数以及设置及其值由至少两个空格或制表符分隔。通常建议使用四空格使分隔符更显式, 在某些情况下, 对齐参数或其他值可能会使数据更易于理解。有关语法的详细信息, 请参阅 "机器人框架用户指南(Robot Framework User Guide)" 。

Higher-level tests
------------------

也可以使用不带位置参数的高级关键字创建测试用例。这种风格允许使用完全免费的文本, 即使与非技术客户或其他项目利益干系人适用通信。当使用 "验收测试驱动开发" (ATDD) 方法或其任何变体和创建的测试作为要求时, 这一点尤其重要。

Robot Framework 机器人框架不强制任何特定的样式来编写测试用例。一种常见的方式是 "由行为驱动的开发" (BDD) 推广的 * given-when-then  * 格式:

.. code:: robotframework

    *** Test Cases ***
    User can change password
        Given a user has a valid account
        When she changes her password
        Then she can log in with the new password
        And she cannot use the old password anymore

__ http://en.wikipedia.org/wiki/Acceptance_test-driven_development
__ http://en.wikipedia.org/wiki/Behavior_driven_development


Data-driven tests
-----------------

很多测试用例通常是相似的, 但它们的输入或输出数据稍有不同。在这些情况下 * 数据驱动测试(data-driven tests) * 允许更改测试数据而不复制工作流。使用Robot Framework的 "[Template]" 设置将测试用例转变为数据驱动测试, 其中模板关键字是使用测试用例正文中定义的数据执行的:

.. code:: robotframework

    *** Test Cases ***
    Invalid password
        [Template]    Creating user with invalid password should fail
        abCD5            ${PWD INVALID LENGTH}
        abCD567890123    ${PWD INVALID LENGTH}
        123DEFG          ${PWD INVALID CONTENT}
        abcd56789        ${PWD INVALID CONTENT}
        AbCdEfGh         ${PWD INVALID CONTENT}
        abCD56+          ${PWD INVALID CONTENT}


除了使用单个测试的 "[Template]" 设置外, 还可以在设置表中一次使用 "Test Template" 设置, 如本指南后面定义的 "setups and teardowns"。在我们的情况下, 这将减轻为无效长度密码案例和其他无效案例创建单独的命名测试。但是, 这需要将这些测试移动到单独的文件中, 因为其它模板也将应用于此文件中的其他测试。

还请注意, 上面示例中的错误消息是使用variables_.


Keywords
========

测试用例是从两个源中的关键字创建的。"库关键字(Library keywords)" 来自导入的测试库, 因此可以使用用于创建测试用例的相同表格语法创建 "用户关键字(user keywords)" 。

Library keywords
----------------

所有最低级别关键字都在测试库中定义, 使用标准编程语言 (通常是 Python 或 Java) 实现。Robot Framework附带了一小部分 ' 测试库 ' _, 可分为标准库 *、* 外部库 * 和 * 自定义库 * (Robot Framework comes with a handful of `test libraries`_ that can be divided to *standard libraries*, *external libraries* and *custom libraries*.)。"标准库(Standard libraries)" _ 与核心框架一起分发, 包括通用库, 如 "操作系统(OperatingSystem)"、"屏幕截图(Screenshot)" 和 "内置(BuiltIn)", 这是特别的, 因为它的关键字是自动可用的。外部库 (如用于 web 测试的 Selenium2Library) 必须单独安装。如果可用的测试库不够, 那么就很容易 "创建自定义测试库"。

为了能够使用由测试库提供的关键字, 必须使用关键字 "Library" 设置导入。本指南中的测试需要从标准的 "操作系统(OperatingSystem)" 库 (例如 "删除文件") 和自定义的 "LoginLibrary" (例如, "尝试使用凭据登录")。这两个库都在下面的设置表中导入:

.. code:: robotframework

    *** Settings ***
    Library           OperatingSystem
    Library           lib/LoginLibrary.py

.. _Test libraries: http://robotframework.org/#libraries
.. _Standard libraries: http://robotframework.org/robotframework/#standard-libraries
.. _Selenium2Library: https://github.com/rtomac/robotframework-selenium2library/#readme
__ `Creating test libraries`_


User keywords
-------------

Robot Framework最强大的功能之一是能够轻松地从其他关键字创建新的更高层次的关键字。创建这些称为 * 用户定义的关键字 * 或 * 用户关键字 * 的语法与用于创建测试用例的语法类似。在此关键字表中创建了以前测试用例中所需的所有高级关键字:

.. code:: robotframework

    *** Keywords ***
    Clear login database
        Remove file    ${DATABASE FILE}

    Create valid user
        [Arguments]    ${username}    ${password}
        Create user    ${username}    ${password}
        Status should be    SUCCESS

    Creating user with invalid password should fail
        [Arguments]    ${password}    ${error}
        Create user    example    ${password}
        Status should be    Creating user failed: ${error}

    Login
        [Arguments]    ${username}    ${password}
        Attempt to login with credentials    ${username}    ${password}
        Status should be    Logged In

	# Keywords below used by higher level tests. Notice how given/when/then/and
    # prefixes can be dropped. And this is a comment.

    A user has a valid account
        Create valid user    ${USERNAME}    ${PASSWORD}

    She changes her password
        Change password    ${USERNAME}    ${PASSWORD}    ${NEW PASSWORD}
        Status should be    SUCCESS

    She can log in with the new password
        Login    ${USERNAME}    ${NEW PASSWORD}

    She cannot use the old password anymore
        Attempt to login with credentials    ${USERNAME}    ${PASSWORD}
        Status should be    Access Denied

用户定义的关键字可以包括由其他用户定义或库关键字定义的操作。从本示例可以看出, 用户定义的关键字可以使用参数。它们还可以返回值, 甚至包含 for 循环。现在, 重要的是要知道的是, 用户定义的关键字使测试创建者能够为常见动作序列创造可重用的步骤。用户定义的关键字还可以帮助测试作者尽可能地保持测试的可读性, 并在不同的情况下使用适当的抽象级别。


Variables
=========

Defining variables
------------------

变量是Robot Framework的一个组成部分。通常, 在受更改的测试中使用的任何数据最好定义为变量。变量定义的语法非常简单, 如本变量表所示:

.. code:: robotframework

    *** Variables ***
    ${USERNAME}               janedoe
    ${PASSWORD}               J4n3D0e
    ${NEW PASSWORD}           e0D3n4J
    ${DATABASE FILE}          ${TEMPDIR}${/}robotframework-quickstart-db.txt
    ${PWD INVALID LENGTH}     Password must be 7-12 characters long
    ${PWD INVALID CONTENT}    Password must be a combination of lowercase and uppercase letters and numbers

也可以从命令行提供变量, 如果需要在不同的环境中执行测试, 则该参数很有用。例如, 可以执行如下demo:

	robot --variable USERNAME:johndoe --variable PASSWORD:J0hnD0e QuickStart.rst

除了用户定义的变量之外, 还有一些内置变量始终可用。这些变量包括在上面的示例中使用的 "$ {TEMPDIR}" 和 "$ {/}"。

Using variables
---------------

变量可以在测试数据的大多数位置使用.它们通常用作关键字的参数, 如下面的测试用例所示。从关键字返回值也可以分配给变量并在以后使用。例如, 下面的 ' Database Should Contain' ' user keyword '  将数据库内容设置为 ' $ {datebase} ' 变量, 然后使用 BuiltIn_ 关键字 "Should Contain" 来验证内容。库和用户关键字都可以返回值。

.. _User keyword: `User keywords`_
.. _BuiltIn: `Standard libraries`_

.. code:: robotframework

    *** Test Cases ***
    User status is stored in database
        [Tags]    variables    database
        Create Valid User    ${USERNAME}    ${PASSWORD}
        Database Should Contain    ${USERNAME}    ${PASSWORD}    Inactive
        Login    ${USERNAME}    ${PASSWORD}
        Database Should Contain    ${USERNAME}    ${PASSWORD}    Active

    *** Keywords ***
    Database Should Contain
        [Arguments]    ${username}    ${password}    ${status}
        ${database} =     Get File    ${DATABASE FILE}
        Should Contain    ${database}    ${username}\t${password}\t${status}\n

Organizing test cases
=====================

Test suites
-----------

测试用例集合被称为Robot Framework中的测试套件。包含测试用例的每个输入文件都形成一个测试套件。当 ' 执行本指南 ' _ 时, 您会看到控制台输出中的测试套件 "QuickStart"。此名称是从文件名派生的, 并且在报表和日志中也可见。

通过将测试用例文件放置到目录中并将这些目录放入其他目录中, 可以分层地组织测试用例。所有这些目录都自动创建更高级别的测试套件, 从目录名获取它们的名称。因为测试套件只是文件和目录, 所以它们被置于任何版本控制系统中。

Setups and teardowns
--------------------

如果希望在每次测试之前或之后执行某些关键字, 请在 "Settings" 表中使用 "Test Setup" 和 "Test Teardown" 设置。同样, 您可以使用 "Test Setup" 和 "Test Teardown" 设置来指定在整个测试套件之前和/或之后执行的关键字。

在测试用例表中, 单个测试还可以使用 "[Setup]" 和 "[Teardown]" 来进行自定义 setup 或 teardown。这与 "[Template]" 之前使用的 "数据驱动测试" 的方法相同。

在本demo中, 我们希望确保在执行开始之前清除数据库, 并且以后每个测试也会清除它:

.. code:: robotframework

    *** Settings ***
    Suite Setup       Clear Login Database
    Test Teardown     Clear Login Database

Using tags
----------

Robot Framework允许为测试用例设置标记, 以便为它们提供free metadata。可以为具有 "强制标记(Force Tags)" 和 "默认标记(Default Tags)" 设置的文件中的所有测试用例设置标记, 如下表所示。还可以使用 "[Tags]" 设置 (如 earlier__ 中的 "用户状态" 存储在数据库测试中) 来定义单个测试用例的标记。

__ `Using variables`_

.. code:: robotframework

    *** Settings ***
    Force Tags        quickstart
    Default Tags      example    smoke

当您在测试执行后查看报表时, 可以看到测试已指定与它们关联的标记, 并且还有基于标记生成的统计信息。标记还可以用于许多其他用途, 其中最重要的是选择要执行的测试的可能性。例如, 您可以尝试以下命令::

	robot --include smoke QuickStart.rst
    robot --exclude database QuickStart.rst


Creating test libraries
=======================

Robot Framework提供了一个简单的 API, 用于使用 Python 或 Java 创建的测试库, 而远程库接口也允许使用其他编程语言。"Robot Framework User Guide" _ 包含有关库 API 的详细说明。

作为一个例子, 我们可以看看这个demo中使用的 "LoginLibrary" 测试库。库位于 "lib/oginlibrary.py" _ 上, 其源代码也将复制到下面.查看您可以看到的代码, 例如, 如何将关键字 "Create User" 映射到方法 "create_user" 的实际实现。

.. code:: python

    import os.path
    import subprocess
    import sys


    class LoginLibrary(object):

        def __init__(self):
            self._sut_path = os.path.join(os.path.dirname(__file__),
                                          '..', 'sut', 'login.py')
            self._status = ''

        def create_user(self, username, password):
            self._run_command('create', username, password)

        def change_password(self, username, old_pwd, new_pwd):
            self._run_command('change-password', username, old_pwd, new_pwd)

        def attempt_to_login_with_credentials(self, username, password):
            self._run_command('login', username, password)

        def status_should_be(self, expected_status):
            if expected_status != self._status:
                raise AssertionError("Expected status to be '%s' but was '%s'."
                                     % (expected_status, self._status))

        def _run_command(self, command, *args):
            command = [sys.executable, self._sut_path, command] + list(args)
            process = subprocess.Popen(command, stdout=subprocess.PIPE,
                                       stderr=subprocess.STDOUT)
            self._status = process.communicate()[0].strip()

---
title:  "Using QtTest Effectively"
date:   2016-10-31 3:33:00
categories:
 - code
tags:
 - qt
 - unittesting
---

Unittesting GUI applications is painful, often slow and with limited testing frameworks available. Although most developers use a design patterns that separate the view from the underlying model, allowing testing of the backend, the resulting test coverage is insufficient for code with many execution paths in the visual layer. QtTest therefore provides tools to unittest both Qt views and models, simplifying test-driven development and GUI debugging.

# QtTest

QtTest is a namespace with methods to [compare data][QCOMPARE], [benchmark tasks][QBENCHMARK], and simulate [mouse clicks][mouseClick] and [key events][keyClick]. QtTest has excellent features, however, the [tutorial](QtTestTutorial) and examples focus on minimizing verbosity, at the expense of scalability.

# Single Test Fixtures

The QtTest tutorial starts by defining a test fixture with private slots for each test method.

```cpp
#include <QtTest/QtTest>

class TestQString: public QObject
{
    Q_OBJECT

private slots:
    void testFromUtf8();
    void testToUtf8();
};
```

We then write our test implementations.

```cpp
void TestQString::testFromUtf8()
{
    std::string str("Hello");
    auto qstr = QString::fromUtf8(str.data());
    QCOMPARE(qstr, QString("Hello"));
}

void TestQString::testToUtf8()
{
    QString qstr("Hello");
    std::string str(qstr.toUtf8().data());
    QCOMPARE(str, std::string("Hello"));
}
```

Finally, we define our entry point.

```cpp
QTEST_MAIN(TestQString)
```

The QMake project can be downloaded [here][SingleFixture].

# Multiple Fixtures

Although this example shows the basis of a testing library, it artificially limits QtTest: the entry point is replaced by a macro that runs a single text fixture. To run multiple fixtures, we need to write our own main function, using a function hidden in the [QtTest reference documentation][qExec]. Now, we can re-write `QTEST_MAIN(TestQString)` with an entry point and `QTest::qExec`.

```cpp
int main(int argc, char *argv[])
{
    return QTest::qExec(new TestQString, argc, argv);
}
```

Supporting multiple test fixtures is now a matter of calling `QTest::qExec` multiple times:

```cpp
int main(int argc, char *argv[])
{
    int status = 0;
    status |= QTest::qExec(new TestQString, argc, argv);
    // status |= QTest::qExec(new TestObject, argc, argv);
    // status |= QTest::qExec(new ..., argc, argv);

    return status;
}
```

# Removing Boilerplate

Defining a lambda expression removes the qExec boilerplate, so the test runner can be invoked with only the class instance.

```cpp
int main(int argc, char *argv[])
{
    // setup lambda
    int status = 0;
    auto runTest = [&status, argc, argv](QObject* obj) {
        status |= QTest::qExec(obj, argc, argv);
    };

    // run suite
    runTest(new TestQString);
    // runTest(new TestObject);
    // runTest(new ...)

    return status;
}
```

# Test Suite

Other unittesting frameworks have concepts of [TestSuites][BoostTestSuite], as well as [unittest discovery][PythonTestDiscover], automatically adding each new test fixture to the unittest tree. In C++, this is difficult to implement, since the test fixture can be instantiated before the test suite. Using a local, static variable defined inside a function, we avoid this static initiation fiasco, and ensure the suite is defined before the fixture. Combining our custom main, the `runTest` lambda, and a test suite, QtTest rivals the ease of use Boost Test Library, GTest, or even Python's unittest.

To create our test suite, we first define a base class with a default constructor and a static function that returns a reference to a collection of test fixtures.

```cpp
#include <QObject>
#include <vector>

/** \brief Base class for the test suite runner.
 */
class TestSuite: public QObject
{
    Q_OBJECT

public:
     TestSuite();

     static std::vector<QObject*> & suite();
};
```

Next, we ensure that the test fixture collection is statically initialized within the function definition, and the constructor calls this function to add itself to the suite.

```cpp
TestSuite::TestSuite()
{
    suite().push_back(this);
}


std::vector<QObject*> & TestSuite::suite()
{
    static std::vector<QObject*> objects;
    return objects;
}
```

We then derive each test fixture from our suite, use the inherited constructor for the suite, and globally initialize our fixture.

```cpp
#include "suite.hpp"

class TestQString: public TestSuite
{
    Q_OBJECT

public:
    using TestSuite::TestSuite;

private slots:
    void testFromUtf8();
    void testToUtf8();
};

void TestQString::testFromUtf8()
{
    std::string str("Hello");
    auto qstr = QString::fromUtf8(str.data());
    QCOMPARE(qstr, QString("Hello"));
}

void TestQString::testToUtf8()
{
    QString qstr("Hello");
    std::string str(qstr.toUtf8().data());
    QCOMPARE(str, std::string("Hello"));
}

static TestQString TEST_QSTRING;
```

Finally, we define our entry point and run the suite.

```cpp
#include "suite.hpp"
#include <QtTest/QtTest>


int main(int argc, char *argv[])
{
    // setup lambda
    int status = 0;
    auto runTest = [&status, argc, argv](QObject* obj) {
        status |= QTest::qExec(obj, argc, argv);
    };

    // run suite
    auto &suite = TestSuite::suite();
    for (auto it = suite.begin(); it != suite.end(); ++it) {
        runTest(*it);
    }
}
```

Running the suite verifies that fixture is added after suite initialization.

```bash
$ ./suite
********* Start testing of TestQString *********
Config: Using QtTest library 5.6.1, Qt 5.6.1 (x86_64-little_endian-lp64 shared (dynamic) release build; by GCC 6.1.1 20160621 (Red Hat 6.1.1-3))
PASS   : TestQString::initTestCase()
PASS   : TestQString::testFromUtf8()
PASS   : TestQString::testToUtf8()
PASS   : TestQString::cleanupTestCase()
Totals: 4 passed, 0 failed, 0 skipped, 0 blacklisted
********* Finished testing of TestQString *********
```

The QMake project can be downloaded [here][TestSuite].

[QtTestTutorial]:      https://doc.qt.io/qt-5/qtest-tutorial.html
[QCOMPARE]:            https://doc.qt.io/qt-5/qtest.html#QCOMPARE
[QBENCHMARK]:          https://doc.qt.io/qt-5/qtest.html#QBENCHMARK
[mouseClick]:          https://doc.qt.io/qt-5/qtest.html#mouseClick
[keyClick]:            https://doc.qt.io/qt-5/qtest.html#keyClick
[SingleFixture]:       {{ site.url }}/attachments/2016-10-31-SingleTestFixture.zip
[TestSuite]:           {{ site.url }}/attachments/2016-10-31-TestSuite.zip
[qExec]:               https://doc.qt.io/qt-5/qtest.html#qExec
[BoostTestSuite]:      http://www.boost.org/doc/libs/1_60_0/libs/test/doc/html/boost_test/intro.html
[PythonTestDiscover]:  https://docs.python.org/2/library/unittest.html#test-discovery

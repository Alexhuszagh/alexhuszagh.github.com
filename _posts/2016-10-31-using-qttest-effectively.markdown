---
title:  "Using QtTest Effectively"
date:   2016-10-31 3:33:00
categories: [qt]
tags: [qt]
---

Unittesting GUI applications is painful, often slow and with limited testing frameworks available. Although most developers use a design patterns that separate the view from the underlying model, allowing testing of the backend, the resulting test coverage is insufficient for code with many execution paths in the visual layer. QtTest therefore provides tools to unittest both Qt views and models, simplifying test-driven development and GUI debugging.

## QtTest

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
#include "qstring.moc"
```

The QMake project file can be downloaded [here][SingleFixture].

# Multiple Fixtures

Although this example shows the basis of a testing library, it artificially limits QtTest: the entry point is replaced by a macro that runs a single unittest. To run multiple unittests in a single compilation unit, we need to write our own main function, which is conveniently buried in the QtTest reference documentation. We also do not know if each unittest class, or unittest function has to be separately invoked, or is automatically discovered.


```cpp
int main(int argc, char *argv[])
{
    return QTest::qExec(new TestQString, argc, argv);
}
```

[QtTestTutorial]:      https://doc.qt.io/qt-5/qtest-tutorial.html
[QCOMPARE]:            https://doc.qt.io/qt-5/qtest.html#QCOMPARE
[QBENCHMARK]:          https://doc.qt.io/qt-5/qtest.html#QBENCHMARK
[mouseClick]:          https://doc.qt.io/qt-5/qtest.html#mouseClick
[keyClick]:            https://doc.qt.io/qt-5/qtest.html#keyClick
[SingleFixture]:       {{ site.url }}/attachments/2016-10-31-SingleTestFixture.zip

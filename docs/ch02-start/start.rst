===========
Get Started
===========

.. sectionauthor:: `jryannel@LinkedIn <https://www.linkedin.com/in/jryannel>`_

.. github:: ch02

.. |creatorrun| image:: assets/qtcreator-run.png

This chapter will introduce you to developing with Qt 5. We will show you how to install the Qt SDK and how you can create as well as run a simple *hello world* application using the Qt Creator IDE.

.. note::

    The source code of this chapter can be found in the `assets folder <../assets>`_.


Installing Qt 5 SDK
===================

.. issues:: ch02

The Qt SDK includes the tools you need to build desktop or embedded applications. You can grab the latest version from the `Qt Company <https://qt.io>`_'s homepage. There is an offline and online installer. The author personally prefers the online installer package as it allows you to install and update multiple Qt releases. This is the recommended way to start. The SDK itself has a maintenance tool, which allows you to update the SDK to the latest version.

The Qt SDK is easy to install and comes with its own IDE for rapid development called *Qt Creator*. The IDE is a highly productive environment for Qt coding and recommended to all readers. Many developers use Qt from the command line, however, and you are free to use the code editor of your choice.

When installing the SDK, you should select the default option and ensure that Qt 5.12 is enabled. Then you're ready to go.

Hello World
===========

.. issues:: ch02

To test your installation, we will create a small *hello world* application. Please, open Qt Creator and create a Qt Quick UI Project ( :menuselection:`File --> New File or Project --> Other Project --> Qt Quick UI prototype` ) and name the project ``HelloWorld``.

.. note::

    The Qt Creator IDE allows you to create various types of applications. If not otherwise stated, we always use a :guilabel:`Qt Quick UI prototype` project.

.. hint::

    A typical Qt Quick application is made out of a runtime called the QmlEngine which loads the initial QML code. The developer can register C++ types with the runtime to interface with the native code. These C++ types can also be bundled into a plugin and then dynamically loaded using an import statement. The ``qmlscene`` and ``qml`` tool are pre-made runtimes, which can be used directly. For the beginning, we will not cover the native side of development and focus only on the QML aspects of Qt 5. This is why we start from a *prototype* project.

Qt Creator creates several files for you. The ``HelloWorld.qmlproject`` file is the project file, where the relevant project configuration is stored. This file is managed by Qt Creator, so don't edit it yourself.

Another file, ``HelloWorld.qml``, is our application code. Open it and try to understand what the application does before you read on.

.. code-block:: js

    // HelloWorld.qml

    import QtQuick 2.12
    import QtQuick.Window 2.12

    Window {
        visible: true
        width: 640
        height: 480
        title: qsTr("Hello World")
    }

The ``HelloWord.qml`` program is written in the QML language. We'll discuss the QML language more in-depth in the next chapter. QML describes the user interface as a tree of hierarchical elements. In this case, a rectangle of 640 x 480 pixels, with a centered text that contains the words "Hello World". To capture user input, a mouse area spans the whole rectangle. When the user interacts with it, the application quits.

To run the application on your own, press the |creatorrun| :guilabel:`Run` tool on the left side, or select :menuselection:`Build > Run` from the menu.

In the background, Qt Creator runs ``qmlscene`` and passes your QML document as the first argument. The ``qmlscene`` application parses the document, and launches the user interface. You should see something like this:

.. figure:: assets/example.png

Qt 5 works! That means we're ready to continue.

.. tip::

    If you are a system integrator, you'll want to have Qt SDK installed to get the latest stable Qt release, as well as a Qt version compiled from source for your specific device target.

.. topic:: Build from Scratch

    If you'd like to build Qt 5 from the command line, you'll first need to grab a copy of the code repository and build it. Visit `Qt's wiki <https://wiki.qt.io/Building_Qt_5_from_Git>`_ for an up-to-date explanation of how to build Qt from git.

    After a successful compilation (and 2 cups of coffee), Qt 5 will be available in the ``qtbase`` folder. Any beverage will suffice, however, we suggest coffee for best results.

    If you want to test your compilation, you can now run the example with the default runtime that comes with Qt 5::

        $ qtbase/bin/qmlscene

Application Types
=================

.. issues:: ch02

This section is a run through of different application types one can write with Qt 5. It's not limited to the selection presented here, but it will give you a better idea of what you can achieve with Qt 5 in general.

Console Application
-------------------

A console application does not provide a graphical user interface, and is usually called as part of a system service or from the command line. Qt 5 comes with a series of ready-made components which help you create cross-platform console applications very efficiently. For example, the networking file APIs, string handling, and an efficient command line parser. As Qt is a high-level API on top of C++, you get programming speed paired with execution speed. Don't think of Qt as being *just* a UI toolkit - it has so much more to offer!

.. rubric:: String Handling

This first example demonstrates how you could add 2 constant strings. Admittedly, this is not a very useful application, but it gives you an idea of what a native C++ application without an event loop may look like.


.. code-block:: cpp

    // module or class includes
    #include <QtCore>

    // text stream is text-codec aware
    QTextStream cout(stdout, QIODevice::WriteOnly);

    int main(int argc, char** argv)
    {
        // avoid compiler warnings
        Q_UNUSED(argc)
        Q_UNUSED(argv)
        QString s1("Paris");
        QString s2("London");
        // string concatenation
        QString s = s1 + " " + s2 + "!";
        cout << s << endl;
    }

.. rubric:: Container Classes

This example adds a list, and list iteration, to the application. Qt comes with a large collection of container classes that are easy to use, and has the same API paradigms as other Qt classes.

.. code-block:: cpp

    QString s1("Hello");
    QString s2("Qt");
    QList<QString> list;
    // stream into containers
    list << s1 << s2;
    // Java and STL like iterators
    QListIterator<QString> iter(list);
    while(iter.hasNext()) {
        cout << iter.next();
        if(iter.hasNext()) {
            cout << " ";
        }
    }
    cout << "!" << endl;

Here is a more advanced list function, that allows you to join a list of strings into one string. This is very handy when you need to proceed line based text input. The inverse (string to string-list) is also possible using the ``QString::split()`` function.

.. code-block:: cpp


    QString s1("Hello");
    QString s2("Qt");
    // convenient container classes
    QStringList list;
    list <<  s1 << s2;
    // join strings
    QString s = list.join(" ") + "!";
    cout << s << endl;


.. rubric:: File IO

In the next snippet, we read a CSV file from the local directory and loop over the rows to extract the cells from each row. Doing this, we get the table data from the CSV file in ca. 20 lines of code. File reading gives us a byte stream, to be able to convert this into valid Unicode text, we need to use the text stream and pass in the file as a lower-level stream. For writing CSV files, you would just need to open the file in write mode, and pipe the lines into the text stream.

.. code-block:: cpp


    QList<QStringList> data;
    // file operations
    QFile file("sample.csv");
    if(file.open(QIODevice::ReadOnly)) {
        QTextStream stream(&file);
        // loop forever macro
        forever {
            QString line = stream.readLine();
            // test for null string 'String()'
            if(line.isNull()) {
                break;
            }
            // test for empty string 'QString("")'
            if(line.isEmpty()) {
                continue;
            }
            QStringList row;
            // for each loop to iterate over containers
            foreach(const QString& cell, line.split(",")) {
                row.append(cell.trimmed());
            }
            data.append(row);
        }
    }
    // No cleanup necessary.

This concludes the section about console based applications with Qt.

C++ Widget Application
----------------------

Console based applications are very handy, but sometimes you need to have a graphical user interface (GUI). In addition, GUI-based applications will likely need a back-end to read/write files, communicate over the network, or keep data in a container.


In this first snippet for widget-based applications, we do as little as needed to create a window and show it. In Qt, a widget without a parent is a window. We use a scoped pointer to ensure that the widget is deleted when the pointer goes out of scope. The application object encapsulates the Qt runtime, and we start the event loop with the ``exec()`` call. From there on, the application reacts only to events triggered by user input (such as mouse or keyboard), or other event providers, such as networking or file IO. The application only exits when the event loop is exited. This is done by calling ``quit()`` on the application or by closing the window.

When you run the code, you will see a window with the size of 240 x 120 pixels. That's all.

.. code-block:: cpp

    #include <QtGui>

    int main(int argc, char** argv)
    {
        QApplication app(argc, argv);
        QScopedPointer<QWidget> widget(new CustomWidget());
        widget->resize(240, 120);
        widget->show();
        return app.exec();
    }

.. rubric:: Custom Widgets

When you work on user interfaces, you may need to create custom-made widgets. Typically, a widget is a window area filled with painting calls. Additionally, the widget has internal knowledge of how to handle keyboard and mouse input, as well as how to react to external triggers. To do this in Qt, we need to derive from `QWidget` and overwrite several functions for painting and event handling.

.. code-block:: cpp

    #ifndef CUSTOMWIDGET_H
    #define CUSTOMWIDGET_H

    #include <QtWidgets>

    class CustomWidget : public QWidget
    {
        Q_OBJECT
    public:
        explicit CustomWidget(QWidget *parent = 0);
        void paintEvent(QPaintEvent *event);
        void mousePressEvent(QMouseEvent *event);
        void mouseMoveEvent(QMouseEvent *event);
    private:
        QPoint m_lastPos;
    };

    #endif // CUSTOMWIDGET_H


In the implementation, we draw a small border on our widget and a small rectangle on the last mouse position. This is very typical for a low-level custom widget. Mouse and keyboard events change the internal state of the widget and trigger a painting update. We won't go into too much detail about this code, but it is good to know that you have the possibility. Qt comes with a large set of ready-made desktop widgets, so it's likely that you don't have to do this.

.. code-block:: cpp


    #include "customwidget.h"

    CustomWidget::CustomWidget(QWidget *parent) :
        QWidget(parent)
    {
    }

    void CustomWidget::paintEvent(QPaintEvent *)
    {
        QPainter painter(this);
        QRect r1 = rect().adjusted(10,10,-10,-10);
        painter.setPen(QColor("#33B5E5"));
        painter.drawRect(r1);

        QRect r2(QPoint(0,0),QSize(40,40));
        if(m_lastPos.isNull()) {
            r2.moveCenter(r1.center());
        } else {
            r2.moveCenter(m_lastPos);
        }
        painter.fillRect(r2, QColor("#FFBB33"));
    }

    void CustomWidget::mousePressEvent(QMouseEvent *event)
    {
        m_lastPos = event->pos();
        update();
    }

    void CustomWidget::mouseMoveEvent(QMouseEvent *event)
    {
        m_lastPos = event->pos();
        update();
    }

.. rubric:: Desktop Widgets

The Qt developers have done all of this for you already and provide a set of desktop widgets, with a native look on different operating systems. Your job, then, is to arrange these different widgets in a widget container into larger panels. A widget in Qt can also be a container for other widgets. This is accomplished through the parent-child relationship. This means we need to make our ready-made widgets, such as buttons, checkboxes, radio buttons, lists, and grids, children of other widgets. One way to accomplish this is displayed below.

Here is the header file for a so-called widget container.

.. code-block:: cpp

    class CustomWidget : public QWidget
    {
        Q_OBJECT
    public:
        explicit CustomWidget(QWidget *parent = 0);
    private slots:
        void itemClicked(QListWidgetItem* item);
        void updateItem();
    private:
        QListWidget *m_widget;
        QLineEdit *m_edit;
        QPushButton *m_button;
    };

In the implementation, we use layouts to better arrange our widgets. Layout managers re-layout the widgets according to some size policies when the container widget is re-sized. In this example, we have a list, a line edit, and a button, which are arranged vertically and allow the user to edit a list of cities. We use Qt's ``signal`` and ``slots`` to connect sender and receiver objects.

.. code-block:: cpp

    CustomWidget::CustomWidget(QWidget *parent) :
        QWidget(parent)
    {
        QVBoxLayout *layout = new QVBoxLayout(this);
        m_widget = new QListWidget(this);
        layout->addWidget(m_widget);

        m_edit = new QLineEdit(this);
        layout->addWidget(m_edit);

        m_button = new QPushButton("Quit", this);
        layout->addWidget(m_button);
        setLayout(layout);

        QStringList cities;
        cities << "Paris" << "London" << "Munich";
        foreach(const QString& city, cities) {
            m_widget->addItem(city);
        }

        connect(m_widget, SIGNAL(itemClicked(QListWidgetItem*)), this, SLOT(itemClicked(QListWidgetItem*)));
        connect(m_edit, SIGNAL(editingFinished()), this, SLOT(updateItem()));
        connect(m_button, SIGNAL(clicked()), qApp, SLOT(quit()));
    }

    void CustomWidget::itemClicked(QListWidgetItem *item)
    {
        Q_ASSERT(item);
        m_edit->setText(item->text());
    }

    void CustomWidget::updateItem()
    {
        QListWidgetItem* item = m_widget->currentItem();
        if(item) {
            item->setText(m_edit->text());
        }
    }

.. rubric:: Drawing Shapes

Some problems are better visualized. If the problem at hand looks remotely like geometrical objects, Qt graphics view is a good candidate. A graphics view arranges simple geometrical shapes in a scene. The user can interact with these shapes, or they are positioned using an algorithm. To populate a graphics view, you need a graphics view and a graphics scene. The scene is attached to the view and is populated with graphics items.
Here is a short example. First the header file with the declaration of the view and scene.

.. code-block:: cpp

    class CustomWidgetV2 : public QWidget
    {
        Q_OBJECT
    public:
        explicit CustomWidgetV2(QWidget *parent = 0);
    private:
        QGraphicsView *m_view;
        QGraphicsScene *m_scene;

    };

In the implementation, the scene gets attached to the view first. The view is a widget and gets arranged in our container widget. In the end, we add a small rectangle to the scene, which is then rendered on the view.

.. code-block:: cpp

    #include "customwidgetv2.h"

    CustomWidget::CustomWidget(QWidget *parent) :
        QWidget(parent)
    {
        m_view = new QGraphicsView(this);
        m_scene = new QGraphicsScene(this);
        m_view->setScene(m_scene);

        QVBoxLayout *layout = new QVBoxLayout(this);
        layout->setMargin(0);
        layout->addWidget(m_view);
        setLayout(layout);

        QGraphicsItem* rect1 = m_scene->addRect(0,0, 40, 40, Qt::NoPen, QColor("#FFBB33"));
        rect1->setFlags(QGraphicsItem::ItemIsFocusable|QGraphicsItem::ItemIsMovable);
    }

Adapting Data
-------------

Up to now, we have mostly covered basic data types and how to use widgets and graphics views. In your applications, you will often need a larger amount of structured data, which may also need to be stored persistently. Finally, the data also needs to be displayed. For this, Qt uses models. A simple model is the string list model, which gets filled with strings and then attached to a list view.

.. code-block:: cpp

    m_view = new QListView(this);
    m_model = new QStringListModel(this);
    view->setModel(m_model);

    QList<QString> cities;
    cities << "Munich" << "Paris" << "London";
    m_model->setStringList(cities);

Another popular way to store and retrieve data is SQL. Qt comes with SQLite embedded, and also has support for other database engines (e.g. MySQL and PostgreSQL). First, you need to create your database using a schema, like this:

.. code-block:: sql

    CREATE TABLE city (name TEXT, country TEXT);
    INSERT INTO city value ("Munich", "Germany");
    INSERT INTO city value ("Paris", "France");
    INSERT INTO city value ("London", "United Kingdom");

To use SQL, we need to add the SQL module to our .pro file

.. code-block:: cpp

    QT += sql

And then we can open our database using C++. First, we need to retrieve a new database object for the specified database engine. With this database object, we open the database. For SQLite, it's enough to specify the path to the database file. Qt provides some high-level database models, one of which is the table model. The table model uses a table identifier and an optional where clause to select the data. The resulting model can be attached to a list view as with the other model before.

.. code-block:: cpp

    QSqlDatabase db = QSqlDatabase::addDatabase("QSQLITE");
    db.setDatabaseName("cities.db");
    if(!db.open()) {
        qFatal("unable to open database");
    }

    m_model = QSqlTableModel(this);
    m_model->setTable("city");
    m_model->setHeaderData(0, Qt::Horizontal, "City");
    m_model->setHeaderData(1, Qt::Horizontal, "Country");

    view->setModel(m_model);
    m_model->select();

For a higher level model operations, Qt provides a sorting file proxy model that allows you sort, filter, and transform models.

.. code-block:: cpp

    QSortFilterProxyModel* proxy = new QSortFilterProxyModel(this);
    proxy->setSourceModel(m_model);
    view->setModel(proxy);
    view->setSortingEnabled(true);

Filtering is done based on the column that is to be filters, and a string as filter argument.

.. code-block:: cpp

    proxy->setFilterKeyColumn(0);
    proxy->setFilterCaseSensitive(Qt::CaseInsensitive);
    proxy->setFilterFixedString(QString)

The filter proxy model is much more powerful than demonstrated here. For now, it is enough to remember it exists.


.. note::

    This has been an overview of the different kind of classic applications you can develop with Qt 5. The desktop is moving, and soon the mobile devices will be our desktop of tomorrow. Mobile devices have a different user interface design. They are much more simplistic than desktop applications. They do one thing and they do it with simplicity and focus. Animations are an important part of the mobile experience. A user interface needs to feel alive and fluent. The traditional Qt technologies are not well suited for this market.

    Coming next: Qt Quick to the rescue.

Qt Quick Application
--------------------

There is an inherent conflict in modern software development. The user interface is moving much faster than our back-end services. In a traditional technology, you develop the so-called front-end at the same pace as the back-end. This results in conflicts when customers want to change the user interface during a project, or develop the idea of a user interface during the project. Agile projects, require agile methods.

Qt Quick provides a declarative environment where your user interface (the front-end) is declared like HTML and your back-end is in native C++ code. This allows you to get the best of both worlds.

This is a simple Qt Quick UI below

.. code-block:: qml

    import QtQuick 2.5

    Rectangle {
        width: 240; height: 1230
        Rectangle {
            width: 40; height: 40
            anchors.centerIn: parent
            color: '#FFBB33'
        }
    }

The declaration language is called QML and it needs a runtime to execute it. Qt provides a standard runtime called ``qmlscene`` but it's also not so difficult to write a custom runtime. For this, we need a quick view and set the main QML document as a source. The only thing left is to show the user interface.

.. code-block:: cpp

    QQuickView* view = new QQuickView();
    QUrl source = QUrl::fromLocalFile("main.qml");
    view->setSource(source);
    view->show();

Let's come back to our earlier examples. In one example, we used a C++ city model. It would be great if we could use this model inside our declarative QML code.

To enable this, we first code our front-end to see how we would want to use a city model. In this case, the front-end expects an object named ``cityModel`` which we can use inside a list view.

.. code-block:: qml

    import QtQuick 2.5

    Rectangle {
        width: 240; height: 120
        ListView {
            width: 180; height: 120
            anchors.centerIn: parent
            model: cityModel
            delegate: Text { text: model.city }
        }
    }

To enable the ``cityModel``, we can mostly re-use our previous model, and add a context property to our root context. The root context is the other root-element in the main document.

.. code-block:: cpp

    m_model = QSqlTableModel(this);
    ... // some magic code
    QHash<int, QByteArray> roles;
    roles[Qt::UserRole+1] = "city";
    roles[Qt::UserRole+2] = "country";
    m_model->setRoleNames(roles);
    view->rootContext()->setContextProperty("cityModel", m_model);

.. hint::

    This is not completely correct, as the SQL table model contains the data in columns and a QML model expects the data as roles. So there needs to be a mapping between columns and roles. See the `QML and QSqlTableModel <http://wiki.qt.io/QML_and_QSqlTableModel>`_ wiki page.

Qt Quick Controls Application
-----------------------------

tbd

Qt Quick Controls Application
-----------------------------

tbd


Summary
=======

.. issues:: ch02

We have seen how to install the Qt SDK and how to create our first application. Then we walked you through the different application types to give you an overview of Qt, showing off some features Qt offers for application development. I hope you got a good impression that Qt is a very rich user interface toolkit and offers everything an application developer can hope for and more. Still, Qt does not lock you into specific libraries, as you can always use other libraries, or even extend Qt yourself. It is also rich when it comes to supporting different application models: console, classic desktop user interface, and touch user interface.

.pro file

QT += quick
QT       += core gui serialport
CONFIG += c++11
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

TARGET = serialGui
TEMPLATE = app


# You can make your code fail to compile if it uses deprecated APIs.
# In order to do so, uncomment the following line.
#DEFINES += QT_DISABLE_DEPRECATED_BEFORE=0x060000    # disables all the APIs deprecated before Qt 6.0.0

SOURCES += \
        main.cpp \
        serialportbt.cpp

RESOURCES += qml.qrc

QML_IMPORT_NAME = io.qt.examples.backend
QML_IMPORT_MAJOR_VERSION = 1

# Additional import path used to resolve QML modules in Qt Creator's code model
QML_IMPORT_PATH =

# Additional import path used to resolve QML modules just for Qt Quick Designer
QML_DESIGNER_IMPORT_PATH =

# Default rules for deployment.
qnx: target.path = /tmp/$${TARGET}/bin
else: unix:!android: target.path = /opt/$${TARGET}/bin
!isEmpty(target.path): INSTALLS += target

HEADERS += \
    serialportbt.h
    
    
.h file

#ifndef SERIALPORTBT_H
#define SERIALPORTBT_H
#include <QtSerialPort/QSerialPort>
#include <QObject>
#include <QDebug>

#include <QObject>
#include <QSerialPortInfo>
#include <QVariant>
#include <QtSerialPort/qserialportinfo.h>


QT_BEGIN_NAMESPACE
namespace Ui
{
class serialportbt;
}
QT_END_NAMESPACE
class serialportbt: public QObject
{
    Q_OBJECT

public:
      explicit serialportbt(QObject *parent = nullptr);
   serialportbt(QWidget *parent = nullptr);
   ~serialportbt();
   
public:
    Q_INVOKABLE static QVariant availablePorts() {
        QList<QSerialPortInfo> portsAvailable = QSerialPortInfo::availablePorts();
        QStringList names_PortsAvailable;
        for(const QSerialPortInfo& portInfo : portsAvailable) {
            names_PortsAvailable<<portInfo.portName();
        }
        return QVariant::fromValue(names_PortsAvailable);
    }
};
   
   
 serialportbt.cpp
 
 #include "serialportbt.h"

#include<QtSerialPort/QSerialPort>
#include <QDebug>
#include <QObject>
serialportbt::serialportbt (QObject * parent):
 QObject(parent)
{
    foreach(QSerialPortInfo port, QSerialPortInfo::availablePorts())
    {
        qDebug()<<port.portName();
    }
}


main.cpp

#include <QGuiApplication>
#include <QQmlApplicationEngine>
#include "serialportbt.h"

int main(int argc, char *argv[])
{
#if QT_VERSION < QT_VERSION_CHECK(6, 0, 0)
    QCoreApplication::setAttribute(Qt::AA_EnableHighDpiScaling);
#endif

    QGuiApplication app(argc, argv);
   qmlRegisterType<serialportbt>("com.main.qml", 1, 0, "serialportbt");

    QQmlApplicationEngine engine;
    const QUrl url(QStringLiteral("qrc:/main.qml"));
    QObject::connect(&engine, &QQmlApplicationEngine::objectCreated,
                     &app, [url](QObject *obj, const QUrl &objUrl) {
        if (!obj && url == objUrl)
            QCoreApplication::exit(-1);
    }, Qt::QueuedConnection);
    engine.load(url);

    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));
    return app.exec();
}


qml file

import QtQuick 2.12
import QtQuick.Window 2.12
import QtQuick.Controls 2.15
import QtMultimedia 5.15
import QtQuick.Layouts 1.0
import QtQuick.Dialogs 1.2
import QtQml 2.2
//import QtQuick.Controls 2.12
//import QtQuick.Controls.Material 2.12
//import Qt.labs.platform 1.0
import QtPositioning 5.2
import QtLocation 5.3
import QtLocation 5.11
import QtLocation 5.15
import QtLocation 5.6
import QtPositioning 5.6
//import com.main.qml
	
Window {
    id: mainwindow
    width: 1350
    height: 800

    visible: true
    color: "#f9f9f9"
    title: qsTr("Hello World")

    Rectangle {
        id: rectanglemap
        x: 215
        y: 86
        width: 1109
        height: 693
        color: "#ffffff"

   Plugin {
       id: osmPlugin
       name: "osm"
       PluginParameter {
                  name: "osm.mapping.providersrepository.disabled"
                  value: "true"
              }
              PluginParameter {
                  name: "osm.mapping.providersrepository.address"
                  value: "http://maps-redirect.qt.io/osm/5.6/"
              }
       }

    Map {
        anchors.fill: parent
        anchors.rightMargin: -15
        anchors.leftMargin: -12
        anchors.topMargin: 0
        plugin: osmPlugin
        center: QtPositioning.coordinate(59.91, 10.75) // Oslo
        zoomLevel: 10


    }
  }

// serialportbt {
//      id:serial

//    }

Rectangle{
       id: targetcoloumn1
    Rectangle {                                             //TARGET box
        id: rectangle
        x: 0
        y: 52
        width: 124
        height: 32
        color: "#f7f6f6"
        radius:10
        Text {
            id: text1
            x: 20
            y: 58
            width: 74
            height: 20
            color: "#de1010"
            text: qsTr("TARGET 1")
            font.pixelSize: 15
            font.bold: true
            font.family: "Arial"
            anchors.centerIn: parent
        }
    }


    RoundButton {                                        //ARM
        id: roundButton6
        x: 130
        y: 52
        width: 68
        height: 29
        text: "ARM"
        font.pointSize: 8
        font.family: "Arial black"
        palette.button: "#4CEB0B"

    }

        RoundButton {                                 //LOITER
        id: button
        x: -1
        y: 84
        width: 65
        height: 23
        text: qsTr("LOITER")
        font.pointSize: 8
        font.family: "Arial black"
        palette.button: "#BDA274"
       }



        RoundButton {                                //NAV
            id: button1
            x: 63
            y: 84
            width: 65
            height: 23
            opacity: 1
            text: qsTr("NAV")
            font.pointSize: 8
            font.family: "Arial black"
            palette.button: "#BDA274"
            onClicked:
                simpledialog.open()

          }
        Dialog{
            id:simpledialog
            title:Message

            Text{
                id:textnav
                text:qsTr("confirm nav ?")
            }

            //StandardButton:StandardButton.ok | StandardButton.cancel
           }


        RoundButton {                           //RTL
            id: button2
            x: 128
            y: 84
            width: 65
            height: 23
            opacity: 1
            text: qsTr("RTL")
            topPadding: 6
            focusPolicy: Qt.StrongFocus
            highlighted: false
            checkable: false
            autoRepeat: false
            font.family: "Arial black"
            font.pointSize: 8
            palette.button: "#FF5B45"
        }

        Rectangle {                                 //telemetry
            id: rectangle2
            x: 1
            y: 109
            width: 124
            height: 28
            color: "#dad0a8"

           }
        Text {
            id: text3
            x: 1
            y: 116
            width: 84
            height: 14
            text: qsTr("TELEMETRY %")
            font.pixelSize: 10
            font.bold: true
        }

        Rectangle {
            id: rectangle3
            x: 76
            y: 113
            width: 45
            height: 20
            color: "#ffffff"
        }

        Text {
            id: text36
            x: 96
            y: 113
            width: 41
            height: 21
            text: qsTr("0")
            font.pixelSize: 15
            font.family: "ARIAL"
        }

        Rectangle {                                          //gps
            id: rectangle4
            x: 127
            y: 109
            width: 69
            height: 28
            color: "#f6320e"

        }

        Text {
            id: text4
            x: 150
            y: 114
            width: 31
            height: 19
            color: "#f2efef"
            text: qsTr("GPS")
            font.pixelSize: 12
            font.bold: true
        }

        Rectangle {                                            //ENGINE
            id: rectangle27
            x: 2
            y: 137
            width: 194
            height: 86
            color: "#b7c5c0"
        }

        Rectangle {
            id: rectangle28
            x: 139
            y: 197
            width: 49
            height: 19
            color: "#ffffff"
        }
        Rectangle {
            id: rectangle29
            x: 76
            y: 196
            width: 49
            height: 19
            color: "#ffffff"
        }
        Rectangle {
            id: rectangle30
            x: 76
            y: 165
            width: 49
            height: 19
            color: "#ffffff"
        }
        Rectangle {
            id: rectangle38
            x: 139
            y: 165
            width: 49
            height: 19
            color: "#ffffff"
        }


        Text {
            id: text29
            x: 160
            y: 196
            width: 17
            height: 20
            text: qsTr("0")
            font.pixelSize: 16
            font.bold: false
        }



        Text {
            id: text30
            x: 94
            y: 196
            width: 15
            height: 20
            text: qsTr("0")
            font.pixelSize: 16
            font.bold: false
        }



        Text {
            id: text37
            x: 94
            y: 165
            width: 15
            height: 20
            text: qsTr("0")
            font.pixelSize: 16
            font.bold: false
        }


        Text {
            id: text38
            x: 160
            y: 164
            width: 15
            height: 20
            text: qsTr("0")
            font.pixelSize: 16
            font.bold: false
        }

        Text {
            id: text39
            x: 10
            y: 139
            width: 60
            height: 27
            text: qsTr("ENGINE")
            font.pixelSize: 12
            font.family: "ARIAL bLACK"
            font.bold: true
        }

        Text {
            id: text40
            x: 16
            y: 168
            width: 48
            height: 20
            text: qsTr("RPM")
            font.pixelSize: 12
            font.bold: true
        }

        Text {
            id: text41
            x: 10
            y: 198
            width: 63
            height: 20
            text: qsTr("EGT (°C)")
            font.pixelSize: 12
            font.bold: true
        }

        Text {
            id: text42
            x: 85
            y: 139
            width: 36
            height: 14
            text: qsTr("LEFT")
            font.pixelSize: 12
            font.bold: true
        }

        Text {
            id: text43
            x: 145
            y: 140
            width: 45
            height: 20
            text: qsTr("RIGHT")
            font.pixelSize: 12
            font.bold: true
        }

        Rectangle {                                         //TIME LEFT
            id: rectangle14
            x: 2
            y: 224
            width: 194
            height: 28
            color: "#b7c5c0"

            Text {
                 id: text14
                 x: 8
                 y: 6
                 text: qsTr("TIME LEFT")
                 font.pixelSize: 12
                 font.bold: true
                 font.family: "ARIAL BLACK"
            }
            Rectangle {
                id: rectangle15
                x: 114
                y: 229
                width: 75
                height: 17
                color: "#ffffff"
                anchors.verticalCenterOffset: 1
                anchors.horizontalCenterOffset: 52
                anchors.centerIn: parent


                Text {
                    id: text17
                    x: 122
                    y: 231
                    width: 87
                    height: 14
                    text: qsTr("00:00:00")
                    font.pixelSize: 15
                    anchors.verticalCenterOffset: -2
                    anchors.horizontalCenterOffset: 14
                    font.bold: false
                    font.family: "arial "
                    anchors.centerIn: parent

                }

            }
         }



       Rectangle {                                  //TIME ELAPSED
           id: rectangle16
           x: 2
           y: 252
           width: 194
           height: 28
           color: "#b7c5c0"
       }

       Rectangle {
           id: rectangle17
           x: 114
           y: 257
           width: 75
           height: 17
           color: "#ffffff"
       }

       Text {
           id: text15
           x: 6
           y: 260
           width: 94
           height: 24
           text: qsTr("TIME ELAPSED")
           font.pixelSize: 12
           font.family: "ARIAL BLACK"
           font.bold: true
       }
       Text {
           id: text18
           x: 122
           y: 256
           width: 87
           height: 20
           text: qsTr("00:00:00")
           font.pixelSize: 15
           font.family: "arial "
           font.bold: false
       }

       Rectangle {                              //FUEL
           id: rectangle18
           x: 2
           y: 280
           width: 194
           height: 28
           color: "#b7c5c0"
       }

       Rectangle {
           id: rectangle19
           x: 114
           y: 285
           width: 75
           height: 17
           color: "#ffffff"
       }

       Text {
           id: text16
           x: 6
           y: 288
           width: 93
           height: 24
           text: qsTr("FUEL (kg)")
           font.pixelSize: 12
           font.family: "ARIAL BLACK"
           font.bold: true
       }

       Text {
           id: text8
           x: 145
           y: 284
           width: 46
           height: 24
           text: qsTr("0")
           font.pixelSize: 15
           font.family: "arial "
       }

       RoundButton {                            //START LEFT
           id: button3
           x: 4
           y: 311
           width: 95
           height: 32
           text: qsTr("START LEFT")
           font.bold: true
           font.pointSize: 8
           font.family: "ARIAL BLACK"
           palette.button: "#05FF8E"

       }

       RoundButton {                              //START RIGHT
           id: button4
           x: 102
           y: 311
           width: 95
           height: 32
           text: qsTr("START RIGHT")
           font.pointSize: 8
           font.bold: true
           font.family: "Arial BLACK"
           palette.button: "#05FF8E"
       }


       Rectangle {                          //HEADING
           id: rectangle20
           x: 3
           y: 346
           width: 193
           height: 28
           color: "#aaffff"
       }

       Rectangle {
           id: rectangle21
           x: 115
           y: 351
           width: 75
           height: 17
           color: "#ffffff"
       }

       Text {
           id: text19
           x: 7
           y: 354
           width: 94
           height: 24
           text: qsTr("HEADING ( ° )")
           font.pixelSize: 12
           font.family: "ARIAL BLACK"
           font.bold: true
       }

       Text {
           id: text21
           x: 148
           y: 351
           width: 46
           height: 24
           text: qsTr("0")
           font.pixelSize: 15
           font.family: "arial "
       }

       Rectangle {                              //AVIONICS
           id: rectangle22
           x: 3
           y: 374
           width: 193
           height: 28
           color: "#aaffff"
       }

       Rectangle {
           id: rectangle23
           x: 115
           y: 379
           width: 75
           height: 17
           color: "#ffffff"
       }

       Text {
           id: text22
           x: 7
           y: 382
           width: 94
           height: 24
           text: qsTr("AVIONICS (V)")
           font.pixelSize: 12
           font.family: "ARIAL BLACK"
           font.bold: true
       }

       Text {
           id: text23
           x: 148
           y: 379
           width: 46
           height: 24
           text: qsTr("0")
           font.pixelSize: 15
           font.family: "arial "
       }


       Rectangle {                              //SERVO
           id: rectangle24
           x: 3
           y: 402
           width: 193
           height: 27
           color: "#aaffff"
       }

       Rectangle {
           id: rectangle25
           x: 115
           y: 407
           width: 75
           height: 17
           color: "#ffffff"
       }

       Text {
           id: text24
           x: 7
           y: 410
           width: 94
           height: 24
           text: qsTr("SERVO (V)")
           font.pixelSize: 12
           font.family: "ARIAL BLACK"
           font.bold: true
       }

       Text {
           id: text25
           x: 148
           y: 407
           width: 46
           height: 24
           text: qsTr("0")
           font.pixelSize: 15
           font.family: "arial "
       }

       Rectangle {                             //SPEED
           id: rectangle26
           x: 3
           y: 430
           width: 193
           height: 70
           color: "#ffff82"
       }

       Rectangle {
           id: rectangle36
           x: 115
           y: 438
           width: 75
           height: 17
           color: "#ffffff"
       }
       Rectangle {
           id: rectangle37
           x: 115
           y: 473
           width: 75
           height: 17
           color: "#ffffff"
       }
       Text {
           id: text26
           x: 4
           y: 456
           width: 46
           height: 19
           text: qsTr("SPEED")
           font.pixelSize: 14
           font.bold: true
           font.family: "ARIEL BLACK"
       }

       Text {
           id: text28
           x: 50
           y: 440
           width: 60
           height: 19
           text: qsTr("TAS(m/s)")
           font.pixelSize: 12
           font.family: "ARIEL BLACK"
           font.bold: true
       }

       Text {
           id: text27
           x: 50
           y: 473
           width: 59
           height: 19
           text: qsTr("GS(m/s)")
           font.pixelSize: 12
           font.family: "ARIEL BLACK"
           font.bold: true
       }
       Text {
           id: text34
           x: 149
           y: 438
           width: 41
           height: 21
           text: qsTr("0")
           font.pixelSize: 15
           font.family: "ARIAL"
       }

           Text {
           id: text35
           x: 149
           y: 473
           width: 41
           height: 21
           text: qsTr("0")
           font.pixelSize: 15
           font.family: "ARIAL"
       }


           Rectangle {                                      //ALTITUDE
               id: rectangle31
               x: 3
               y: 500
               width: 193
               height: 59
               color: "#9aa38c"
           }

           Rectangle {
               id: rectangle32
               x: 115
               y: 509
               width: 75
               height: 17
               color: "#ffffff"
           }

           Text {
               id: text31
               x: 7
               y: 517
               width: 99
               height: 25
               text: qsTr("ALTITUDE (m)")
               font.pixelSize: 13
               font.family: "arial black"
               font.bold: true
           }

           Text {
               id: text32
               x: 149
               y: 509
               width: 41
               height: 21
               text: qsTr("0")
               font.pixelSize: 15
               font.family: "ARIAL"
           }

           Rectangle {
               id: rectangle35
               x: 115
               y: 532
               width: 75
               height: 17
               color: "#ffffff"
           }
    }

        RoundButton {
        id: roundButtonopen
        x: 198
        y: 49
        width:20
        height:30
        Image {
            id: arrow
            source: "image/right arrow.jpg"
            anchors.fill: parent
        }
        onClicked: {
            targetcolumn2.visible=true
            roundButtonopen.visible=false
            roundButtonclose.visible=true
        }

    }
        RoundButton {
            id: roundButtonclose
            x: 398
            y: 50
            width: 18
            height: 28
            visible: false
            Image {
                id: arrow1
                 source: "image/closearrow.png"
                anchors.fill: parent

         }
            onClicked: {
                targetcolumn2.visible=false
                roundButtonopen.visible=true
                roundButtonclose.visible=false
            }

        }

        Rectangle{
            id:targetcolumn2
            visible: false

            Rectangle {                             //TARGET2
                id: rectangle1
                x: 199
                y: 52
                width: 124
                height: 32
                color: "#f7f6f6"
                radius: 10
                Text {
                    id: text2
                    x: 20
                    y: 58
                    width: 74
                    height: 20
                    color: "#de1010"
                    text: qsTr("TARGET 2")
                    font.pixelSize: 15
                    font.family: "Arial"
                    anchors.centerIn: parent
                    font.bold: true
                }
            }

            RoundButton {                           //ARM
                id: roundButton8
                x: 329
                y: 52
                width: 68
                height: 29
                text: "ARM"
                font.family: "Arial black"
                palette.button: "#4ceb0b"
                font.pointSize: 8
            }

            RoundButton {                           //LOITER
                id: button5
                x: 198
                y: 84
                width: 65
                height: 23
                text: qsTr("LOITER")
                font.family: "Arial black"
                palette.button: "#bda274"
                font.pointSize: 8
            }

            RoundButton {                           //NAV
                id: button7
                x: 262
                y: 84
                width: 65
                height: 23
                opacity: 1
                text: qsTr("NAV")
                font.family: "Arial black"
                palette.button: "#bda274"
                onClicked: simpledialog.open()
                font.pointSize: 8
            }

            RoundButton {                            //RTL
                id: button8
                x: 327
                y: 84
                width: 65
                height: 23
                opacity: 1
                text: qsTr("RTL")
                focusPolicy: Qt.StrongFocus
                font.family: "Arial black"
                palette.button: "#ff5b45"
                font.pointSize: 8
                topPadding: 6
                autoRepeat: false
                checkable: false
                highlighted: false
            }

            Rectangle {                               //TELEMETRY
                id: rectangle6
                x: 200
                y: 109
                width: 124
                height: 28
                color: "#dad0a8"
            }

            Text {
                id: text5
                x: 200
                y: 116
                width: 84
                height: 14
                text: qsTr("TELEMETRY %")
                font.pixelSize: 10
                font.bold: true
            }

            Rectangle {
                id: rectangle7
                x: 275
                y: 113
                width: 45
                height: 20
                color: "#ffffff"
            }

            Text {
                id: text44
                x: 295
                y: 113
                width: 41
                height: 21
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "ARIAL"
            }

            Rectangle {                                 //GPS
                id: rectangle8
                x: 326
                y: 109
                width: 69
                height: 28
                color: "#f6320e"
            }

            Text {
                id: text6
                x: 349
                y: 114
                width: 31
                height: 19
                color: "#f2efef"
                text: qsTr("GPS")
                font.pixelSize: 12
                font.bold: true
            }

            Rectangle {                                 //ENGINE
                id: rectangle33
                x: 201
                y: 137
                width: 194
                height: 86
                color: "#b7c5c0"
            }

            Rectangle {
                id: rectangle34
                x: 338
                y: 197
                width: 49
                height: 19
                color: "#ffffff"
            }

            Rectangle {
                id: rectangle39
                x: 275
                y: 196
                width: 49
                height: 19
                color: "#ffffff"
            }

            Rectangle {
                id: rectangle40
                x: 275
                y: 165
                width: 49
                height: 19
                color: "#ffffff"
            }

            Rectangle {
                id: rectangle41
                x: 338
                y: 165
                width: 49
                height: 19
                color: "#ffffff"
            }

            Text {
                id: text33
                x: 359
                y: 196
                width: 17
                height: 20
                text: qsTr("0")
                font.pixelSize: 16
                font.bold: false
            }

            Text {
                id: text45
                x: 293
                y: 196
                width: 15
                height: 50
                text: qsTr("0")
                font.pixelSize: 16
                font.bold: false
            }

            Text {
                id: text46
                x: 293
                y: 165
                width: 15
                height: 20
                text: qsTr("0")
                font.pixelSize: 16
                font.bold: false
            }

            Text {
                id: text47
                x: 359
                y: 164
                width: 15
                height: 20
                text: qsTr("0")
                font.pixelSize: 16
                font.bold: false
            }

            Text {
                id: text48
                x: 209
                y: 139
                width: 60
                height: 27
                text: qsTr("ENGINE")
                font.pixelSize: 12
                font.family: "ARIAL bLACK"
                font.bold: true
            }

            Text {
                id: text49
                x: 215
                y: 168
                width: 48
                height: 20
                text: qsTr("RPM")
                font.pixelSize: 12
                font.bold: true
            }

            Text {
                id: text50
                x: 209
                y: 198
                width: 63
                height: 20
                text: qsTr("EGT (°C)")
                font.pixelSize: 12
                font.bold: true
            }

            Text {
                id: text51
                x: 284
                y: 139
                width: 36
                height: 14
                text: qsTr("LEFT")
                font.pixelSize: 12
                font.bold: true
            }

            Text {
                id: text52
                x: 344
                y: 140
                width: 45
                height: 20
                text: qsTr("RIGHT")
                font.pixelSize: 12
                font.bold: true
            }

            Rectangle {                                     //TIME LEFT
                id: rectangle42
                x: 201
                y: 224
                width: 194
                height: 28
                color: "#b7c5c0"
                Text {
                    id: text20
                    x: 8
                    y: 6
                    text: qsTr("TIME LEFT")
                    font.pixelSize: 12
                    font.family: "ARIAL BLACK"
                    font.bold: true
                }

                Rectangle {
                    id: rectangle43
                    x: 114
                    y: 229
                    width: 75
                    height: 17
                    color: "#ffffff"
                    Text {
                        id: text53
                        x: 122
                        y: 231
                        width: 87
                        height: 14
                        text: qsTr("00:00:00")
                        font.pixelSize: 15
                        font.family: "arial "
                        anchors.centerIn: parent
                        anchors.verticalCenterOffset: -2
                        font.bold: false
                        anchors.horizontalCenterOffset: 14
                    }
                    anchors.centerIn: parent
                    anchors.verticalCenterOffset: 1
                    anchors.horizontalCenterOffset: 52
                }
            }

            Rectangle {                                      //TIME ELAPSED
                id: rectangle44
                x: 201
                y: 252
                width: 194
                height: 28
                color: "#b7c5c0"
            }

            Rectangle {
                id: rectangle45
                x: 313
                y: 257
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text54
                x: 205
                y: 260
                width: 94
                height: 24
                text: qsTr("TIME ELAPSED")
                font.pixelSize: 12
                font.family: "ARIAL BLACK"
                font.bold: true
            }

            Text {
                id: text55
                x: 321
                y: 256
                width: 87
                height: 20
                text: qsTr("00:00:00")
                font.pixelSize: 15
                font.family: "arial "
                font.bold: false
            }

            Rectangle {                                     //FUEL
                id: rectangle46
                x: 201
                y: 280
                width: 194
                height: 28
                color: "#b7c5c0"
            }

            Rectangle {
                id: rectangle47
                x: 313
                y: 285
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text56
                x: 205
                y: 288
                width: 93
                height: 24
                text: qsTr("FUEL (kg)")
                font.pixelSize: 12
                font.family: "ARIAL BLACK"
                font.bold: true
            }

            Text {
                id: text9
                x: 344
                y: 284
                width: 46
                height: 24
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "arial "
            }

            RoundButton {                               //START LEFT
                id: button9
                x: 203
                y: 311
                width: 95
                height: 32
                text: qsTr("START LEFT")
                font.family: "ARIAL BLACK"
                palette.button: "#05ff8e"
                font.bold: true
                font.pointSize: 8
            }

            RoundButton {                                   //START RIGHT
                id: button10
                x: 301
                y: 311
                width: 95
                height: 32
                text: qsTr("START RIGHT")
                font.family: "Arial BLACK"
                palette.button: "#05ff8e"
                font.bold: true
                font.pointSize: 8
            }

            Rectangle {                                         //HEADING
                id: rectangle48
                x: 202
                y: 346
                width: 193
                height: 28
                color: "#aaffff"
            }

            Rectangle {
                id: rectangle49
                x: 314
                y: 351
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text57
                x: 206
                y: 354
                width: 94
                height: 24
                text: qsTr("HEADING ( ° )")
                font.pixelSize: 12
                font.family: "ARIAL BLACK"
                font.bold: true
            }

            Text {
                id: text58
                x: 347
                y: 351
                width: 46
                height: 24
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "arial "
            }

            Rectangle {                                     //AVIONICS
                id: rectangle50
                x: 202
                y: 374
                width: 193
                height: 28
                color: "#aaffff"
            }

            Rectangle {
                id: rectangle51
                x: 314
                y: 379
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text59
                x: 206
                y: 382
                width: 94
                height: 24
                text: qsTr("AVIONICS (V)")
                font.pixelSize: 12
                font.family: "ARIAL BLACK"
                font.bold: true
            }

            Text {
                id: text60
                x: 347
                y: 379
                width: 46
                height: 24
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "arial "
            }

            Rectangle {                             //SERVO
                id: rectangle52
                x: 202
                y: 402
                width: 193
                height: 27
                color: "#aaffff"
            }

            Rectangle {
                id: rectangle53
                x: 314
                y: 407
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text61
                x: 206
                y: 410
                width: 94
                height: 24
                text: qsTr("SERVO (V)")
                font.pixelSize: 12
                font.family: "ARIAL BLACK"
                font.bold: true
            }

            Text {
                id: text62
                x: 347
                y: 407
                width: 46
                height: 24
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "arial "
            }

            Rectangle {                                    //SPEED
                id: rectangle54
                x: 202
                y: 430
                width: 193
                height: 70
                color: "#ffff82"
            }

            Rectangle {
                id: rectangle55
                x: 314
                y: 438
                width: 75
                height: 17
                color: "#ffffff"
            }

            Rectangle {
                id: rectangle56
                x: 314
                y: 473
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text63
                x: 203
                y: 456
                width: 46
                height: 19
                text: qsTr("SPEED")
                font.pixelSize: 14
                font.family: "ARIEL BLACK"
                font.bold: true
            }

            Text {
                id: text64
                x: 249
                y: 440
                width: 60
                height: 19
                text: qsTr("TAS(m/s)")
                font.pixelSize: 12
                font.family: "ARIEL BLACK"
                font.bold: true
            }

            Text {
                id: text65
                x: 249
                y: 473
                width: 59
                height: 19
                text: qsTr("GS(m/s)")
                font.pixelSize: 12
                font.family: "ARIEL BLACK"
                font.bold: true
            }

            Text {
                id: text66
                x: 348
                y: 438
                width: 41
                height: 21
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "ARIAL"
            }

            Text {
                id: text67
                x: 348
                y: 473
                width: 41
                height: 21
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "ARIAL"
            }

            Rectangle {                                         //ALTITUDE
                id: rectangle57
                x: 202
                y: 500
                width: 193
                height: 59
                color: "#9aa38c"
            }

            Rectangle {
                id: rectangle58
                x: 314
                y: 509
                width: 75
                height: 17
                color: "#ffffff"
            }

            Text {
                id: text68
                x: 206
                y: 517
                width: 99
                height: 25
                text: qsTr("ALTITUDE (m)")
                font.pixelSize: 13
                font.family: "arial black"
                font.bold: true
            }

            Text {
                id: text69
                x: 348
                y: 509
                width: 41
                height: 21
                text: qsTr("0")
                font.pixelSize: 15
                font.family: "ARIAL"
            }

            Rectangle {
                id: rectangle59
                x: 314
                y: 532
                width: 75
                height: 17
                color: "#ffffff"
            }


      }


        ComboBox {                                          //TARGET 1 & 2 DROPBOX
            id: comboBox
            x: 702
            width: 117
            height: 27

            anchors.top: parent.top
            font.bold: true
            font.family: "arial black"
            anchors.topMargin: 51
            model: ["TARGET 1","TARGET 2"]
             background: Rectangle {
                 color:"#D1BDA8"
             }
        }

     RoundButton {                                              //ACTIONS
            id: roundButton
            x: 825
            y: 51
            width: 99
            height: 28
            text: "ACTIONS"
            z: 0
            hoverEnabled: true
            flat: false
            font.pointSize: 8
            font.family: "ARIAL BLACK"
            palette.button:"#FF8C4B"
            onClicked:{
                rectact.visible=true
            }

            Rectangle{
                id:rectact
                x:-27
                y:34
                width:153
                height:200
                visible: false
                color: "#ffffff"
                Button{
                    id:actclose
                    x:120
                    y:1
                    width: 31
                    height: 25
                    palette.button: "white"
                    Image {
                        id: actcross
                        source: "image/close.png"
                        anchors.rightMargin: 0
                        anchors.bottomMargin: 0
                        anchors.leftMargin: 0
                        anchors.topMargin: 0
                        anchors.fill:parent
                    }
                    onClicked: {
                        rectact.visible=false
                    }
                }

                Text {
                    id: textact
                    x: 54
                    y: 74
                    width: 45
                    height: 20
                    text: qsTr("SET WP")
                    font.pixelSize: 16
                }

                Rectangle{
                    id:rectaction
                    x:3
                    y:1
                    width:116
                    height:25
                    color:"#FF8C4B"

                    Text {
                        id: textactin
                        text: qsTr("ACTIONS")
                        x: 22
                        y: 4
                        width: 45
                        height: 20
                        font.family: "ARIAL BLACK"
                    }

                }
                ComboBox{
                    id: comboBoxact
                    x:4
                    y:32
                    width: 148
                    height: 25

                    font.family: "arial"

                    model: ["AUTO","MANUAL","GUIDED","AUTOLEVEL","COORDINATED","COORDINATE_AUTO...","STABILIZE_EARTH"]
                  }


                    TextField {
                        placeholderText: qsTr("  ")
                        x:46
                        y:100
                        width: 65
                        height: 30
                        cursorVisible: TRUE
                    }
                    RoundButton{
                        id: roundButtonact
                        x:36
                        y:150
                        width: 85
                        height: 24
                        text: "SIM"
                        font.family: "ARIAL BLACK"
                        font.pointSize: 8
                        palette.button:"#FF8C4B"

                    }



               }

        }


        RoundButton {                                       //SUB-SCALE
            id: roundButton1
            x: 930
            y: 52
            width: 99
            height: 28
            text: "SUB -SCALE"
            font.family: "ARIAL BLACK"
            font.pointSize: 8
            palette.button:"#FF915E"

           onClicked: {
               rectanglesubscale.visible=true
           }


            Rectangle{
                id:rectanglesubscale
                x: -27
                y: 31
                width:153
                height:200
                visible: false
                color: "#fdfdfd"
                Button{
                    id:subclose
                    x: 120
                    y: 0
                    width: 33
                    height: 26
                    palette.button: "white"
                    Image {
                        id: subcross
                        source: "image/close.png"
                        anchors.fill: parent
                    }
                    onClicked: {
                        rectanglesubscale.visible=false
                    }

                }

                Text {
                    id: textsubscale
                    x: 19
                    y: 32
                    width: 45
                    height: 20
                    text: qsTr("REF QNH")
                    font.pixelSize: 16
                }
                RoundButton{
                    id: roundButtonsubscale
                    x:40
                    y:64
                    width: 85
                    height: 24
                    text: "SET QNH"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#FF915E"

                }
                Rectangle{

                        id: rectanglesub
                        x: 95
                        y: 31
                        width: 49
                        height: 21
                        color: "#ffffff"
                        border.color:"black"
                    }

            Text {
                    id: textsubscale2
                    x: 19
                    y: 105
                    width: 45
                    height: 20
                    text: qsTr("REF ALT")
                    font.pixelSize: 16
                }

                RoundButton{
                    id: roundButtonsubscale2
                    x:42
                    y:146
                    width: 85
                    height: 24
                    text: "SET ALT"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#FF915E"

                }
                Rectangle{

                        id: rectanglesub2
                        x: 95
                        y: 107
                        width: 49
                        height: 21
                        color: "#ffffff"
                        border.color:"black"
                        rotation: -0.07
                    }

                Rectangle{
                    id:rectsubscale
                    x:0
                    y:0
                    width:120
                    height:26
                    color:"#FF915E"

                    Text {
                        id: textsubscale3
                        text: qsTr("SUB-SCALE")
                        x: 26
                        y: 3
                        width: 79
                        height: 20
                        font.family: "ARIAL BLACK"
                    }

                }

            }

        }

        RoundButton {                                       //PARAMETERS
            id: roundButton2
            x: 1035
            y: 51
            width: 99
            height: 28
            text: "PARAMETERS"
            font.family: "ARIAL BLACK"
            font.pointSize: 8
            palette.button:"#FFAA7F"

             onClicked: {
                 rectparameter.visible=true
             }


                Rectangle{
                    id:rectparameter
                    x: -25
                    y: 37
                    width:150
                    height:400
                    visible: false
                    color: "#fbfbfb"
                    Button{
                        id:paraclose
                        x: 121
                        y: 0
                        width:30
                        height: 25
                        palette.button:"white"
                        Image {
                            id: paracross
                            source: "image/close.png"
                            anchors.rightMargin: 0
                            anchors.bottomMargin: 0
                            anchors.leftMargin: 0
                            anchors.topMargin: 0
                            anchors.fill: parent
                        }
                        onClicked: {
                            rectparameter.visible=false

                        }

                    }

                 Rectangle{
                     id:rectpara
                     x:0
                     y:0
                     width:115
                     height:25
                     color:"#FFAA7F"

                     Text {
                         id: textpara2
                         text: qsTr("PARAMETERS")
                         x: 14
                         y: 5
                         width: 45
                         height: 20
                         font.family: "ARIAL BLACK"
                     }

                 }

                 Text {
                     id: textpara
                     x:6
                     y: 36
                     width: 45
                     height: 20
                     text: qsTr("SEARCH:")
                     font.pixelSize: 12
                 }


                 TextField {
                     id:textfieldpara
                     placeholderText: qsTr("  ")
                     x:23
                     y:60
                     width:100
                     height: 30
                     cursorVisible: TRUE
                 }
                 RoundButton{
                     id: roundButtonpara
                     x:3
                     y:101
                     width: 70
                     height: 24
                     text: "SEARCH"
                     font.family: "ARIAL BLACK"
                     font.pointSize: 8
                     palette.button:"#FFAA7F"

                 }
                 RoundButton{
                     id: roundButtonpara2
                     x:80
                     y:101
                     width: 70
                     height: 24
                     text: "CLEAR"
                     font.family: "ARIAL BLACK"
                     font.pointSize: 8
                     palette.button:"#FFAA7F"
                     onClicked: {
                                      textfieldpara.text = "";
                                  }
                 }

             }
        }

        RoundButton {
            id: roundButton3
            x: 1140
            y: 50
            width: 99
            height: 28
            text: "WP MANAGER"
            font.family: "ARIAL BLACK"
            font.pointSize: 8
            palette.button:"#FFBF94"
            onClicked:{
                rectanglewpmanager.visible=true
             }


            Rectangle{
                id:rectanglewpmanager
                x: -50
                y: 37
                width:210
                height:368
                color:"#ffffff"
                visible: false
                 Button{
                    id:wpclose
                    x: 177
                    y: 0
                    width:33
                    height:25
                    palette.button: "white"
                    Image {
                        id: wpcross
                        source: "image/close.png"
                        anchors.rightMargin: 0
                        anchors.bottomMargin: 0
                        anchors.leftMargin: 0
                        anchors.topMargin: 0
                        anchors.fill:parent
                    }
                    onClicked: {
                        rectanglewpmanager.visible=false
                    }

                }


                 Rectangle{
                     id:rectwp
                     x:0
                     y:0
                     width:177
                     height:25
                     color:"#FFBF94"

                     Text {
                         id: textwp
                         text: qsTr("WP MANAGER")
                         x: 46
                         y: 4
                         width: 40
                         height: 20
                         font.family: "ARIAL BLACK"
                     }

                 }
                 RoundButton{
                     id: roundButtonwp1
                     x:13
                     y:42
                     width: 85
                     height: 24
                     text: "WRITE WP"
                     font.family: "ARIAL BLACK"
                     font.pointSize: 8
                     palette.button:"#FFBF94"

                 }
                 RoundButton{
                     id: roundButtonWP2
                     x:112
                     y:42
                     width: 85
                     height: 24
                     text: "READ WP"
                     font.family: "ARIAL BLACK"
                     font.pointSize: 8
                     palette.button:"#FFBF94"

                 }
                 Text {
                     id: textwp2
                     x:13
                     y:87
                     width: 45
                     height: 20
                     text: qsTr("WP SELECTED")
                     font.pixelSize: 12
                 }
                 Rectangle{

                         id: rectanglewp
                         x: 115
                         y: 87
                         width: 49
                         height: 21
                         color: "#ffffff"
                         border.color:"black"
                     }
                 Text {
                     id: textwp3
                     x:11
                     y: 124
                     width: 45
                     height: 20
                     text: qsTr("WP RADIUS")
                     font.pixelSize: 12
                 }


                 TextField {
                     placeholderText: qsTr("  ")
                     x:115
                     y:119
                     width:87
                     height: 25
                     cursorVisible: true
                 }
                 Text {
                     id: textwp4
                     x:13
                     y:165
                     width: 45
                     height: 20
                     text: qsTr("SPEED")
                     font.pixelSize: 12
                 }


                 TextField {
                     placeholderText: qsTr("  ")
                     x:115
                     y:160
                     width:87
                     height: 25
                 }
                 Text {
                     id: textwp5
                     x:15
                     y:205
                     width: 45
                     height: 20
                     text: qsTr("LAT")
                     font.pixelSize: 12
                 }
                 Rectangle{

                         id: rectanglewp2
                         x: 115
                         y: 204
                         width: 49
                         height: 21
                         color: "#ffffff"
                         border.color:"black"
                     }
                 Text {
                     id: textwp6
                     x:15
                     y:246
                     width: 45
                     height: 20
                     text: qsTr("LNG")
                     font.pixelSize: 12
                 }
                 Rectangle{

                         id: rectanglewp3
                         x: 115
                         y: 245
                         width: 49
                         height: 21
                         color: "#ffffff"
                         border.color:"black"
                     }
                 Text {
                     id: textwp7
                     x:13
                     y: 286
                     width: 45
                     height: 20
                     text: qsTr("ALT")
                     font.pixelSize: 12
                 }


                 TextField {
                     placeholderText: qsTr("  ")
                     x:115
                     y:286
                     width:87
                     height: 25
                     cursorVisible: TRUE
                 }
                 RoundButton{
                     id: roundButtonWP3
                     x:61
                     y:328
                     width: 85
                     height: 24
                     text: "RESET"
                     font.family: "ARIAL BLACK"
                     font.pointSize: 8
                     palette.button:"#FFBF94"

                 }

           }
        }

        RoundButton {                                               //MISSION
            id: roundButton4
            x: 1245
            y: 50
            width: 99
            height: 28
            text: "MISSION"
            font.family: "ARIAL BLACK"
            font.pointSize: 8
            palette.button:"#FFD2B4"
            onClicked:{

                missionwindow.visible = true
                mainwindow.visible = false
            }

           Window{
                id:missionwindow
                visible:false

                width: 1350
                height: 800
                title: qsTr("mission")
               Rectangle{
                   id:missionmap
                   width: 1350
                   height: 500

                   Plugin {
                       id: osmPlugin2
                       name: "osm"
                       PluginParameter {
                                  name: "osm.mapping.providersrepository.disabled"
                                  value: "true"
                              }
                              PluginParameter {
                                  name: "osm.mapping.providersrepository.address"
                                  value: "http://maps-redirect.qt.io/osm/5.6/"
                              }
                       }

                    Map {
                        anchors.fill: parent
                        plugin: osmPlugin
                        center: QtPositioning.coordinate(59.91, 10.75) // Oslo
                        zoomLevel: 10


                    }


               }

                Rectangle {
                    id: rectangle5miss
                    x: 1
                    y: 0
                    width: 1349
                    height: 46
                    color: "#e1dddd"
                }
                RoundButton {
                    id:read
                    x: 10
                    y: 51
                    width: 110
                    height: 28
                    text: "READ MISSION"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#98AFC7"
            }
                RoundButton {
                    id:write
                    x: 130
                    y: 51
                    width: 110
                    height: 28
                    text: "WRITE MISSION"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#87CEFA"
            }
                RoundButton {
                    id:load
                    x: 250
                    y: 51
                    width: 110
                    height: 28
                    text: "LOAD MISSION"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#ADDFFF"
            }
                RoundButton {
                    id:save
                    x: 370
                    y: 51
                    width: 110
                    height: 28
                    text: "SAVE MISSION"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#BDEDFF"
            }
                RoundButton {
                    id:clear
                    x: 490
                    y: 51
                    width: 110
                    height: 28
                    text: "CLEAR MISSION"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    palette.button:"#C9DFEC"
            }
                RoundButton {
                    id: roundButton5miss
                    x: 820
                    y: 12
                    width: 96
                    height: 25
                    text: "SIM"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 11
                    palette.button: "#D1BDA8"
                }

                ComboBox {
                    id: comboBox1miss
                    x: 941
                    y: 12
                    width: 140
                    height: 25
                    displayText: "PORT"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    background: Rectangle {
                        color:"#FFFFFF"
                    }

                }

                ComboBox {
                    id: comboBox2miss
                    x: 1099
                    y: 12
                    width: 140
                    height: 25
                    displayText: "BAUDRATE"
                    font.family: "ARIAL BLACK"
                    font.pointSize: 8
                    background: Rectangle {
                        color:"#FFFFFF"
                    }
                }



                Button {
                    id: button6miss
                    x: 1252
                    y: 4
                    width: 82
                    height: 40


                    Image {
                           id: bgimgmiss
                           width:82
                           height:40
                           smooth: true
                           anchors.top: button.top
                           anchors.bottom: innerText.top
                           fillMode: Image.PreserveAspectFit
                           horizontalAlignment: Image.AlignHCenter
                           source: "qrc:/image/connect.png"
                    }
                }


                Button {
                    id: buttonmiss
                    x: 90
                    y: 4
                    width: 82
                    height: 40

                    Image {
                           id: setimgmiss
                           width:82
                           height:40
                           smooth: true
                           anchors.top: button.top
                           anchors.bottom: innerText.top
                           fillMode: Image.PreserveAspectFit
                           horizontalAlignment: Image.AlignHCenter
                           source: "qrc:/image/settings.png"
                    }

                }
                Button {
                    id: buttonlogomiss
                    x: 1
                    y: 4
                    width: 82
                    height: 40

                    Image {
                           id: logomiss
                           width:82
                           height:40
                           smooth: true
                           anchors.top: button.top
                           anchors.bottom: innerText.top
                           fillMode: Image.PreserveAspectFit
                           horizontalAlignment: Image.AlignHCenter
                           source: "qrc:/image/idslogo.PNG"
                    }
                    onClicked: {

                          mainwindow.visible=true
                        missionwindow.visible=false
                    }

               }

}


        Rectangle {
            id: rectangle5
            x: -1244
            y: -48
            width: 1349
            height: 46
            color: "#e1dddd"

          }

        RoundButton {
            id: roundButton5
            x: -451
            y: -37
            width: 96
            height: 25
            text: "SIM"
            font.family: "ARIAL BLACK"
            font.pointSize: 11
            palette.button: "#D1BDA8"
        }

        ComboBox {
            id: comboBox1
            x: -317
            y: -37
            width: 140
            height: 25
            displayText: "PORT"
            font.family: "ARIAL BLACK"
            font.pointSize: 8
            background: Rectangle {
                color:"#FFFFFF"
            }

        }

        ComboBox {
            id: comboBox2
            x: -160
            y: -37
            width: 140
            height: 25
            displayText: "BAUDRATE"
            font.family: "ARIAL BLACK"
            font.pointSize: 8
            background: Rectangle {
                color:"#FFFFFF"
            }
        }



        Button {
            id: buttonconnect
            x: 11
            y: -47
            width: 82
            height: 40
            palette.button:"white"

            Image {
                   id: bgimg
                   x: 3
                   y: 1
                   width:82
                   height:40
                   smooth: true
                   anchors.fill: parent
                   anchors.top: button.top
                   anchors.bottom: innerText.top
                   fillMode: Image.PreserveAspectFit
                   horizontalAlignment: Image.AlignHCenter
                   source: "qrc:/image/connect.png"
            }
        }


        Button {
            id: buttonset
            x: -1144
            y: -45
            width: 82
            height: 40
            palette.button:"white"
            onClicked: {
                settingswindow.visible=true
                mainwindow.visible=false
            }

            Image {
                   id: setimg
                   x: -2
                   y: 0
                   width:82
                   height:40
                   smooth: true
                   anchors.fill: parent
                   anchors.top: button.top
                   anchors.bottom: innerText.top
                   fillMode: Image.PreserveAspectFit
                   horizontalAlignment: Image.AlignHCenter
                   source: "qrc:/image/settings.png"
                   anchors.rightMargin: 0
                   anchors.bottomMargin: 0
                   anchors.leftMargin: 0
                   anchors.topMargin: 1
            }
            Window{
                 id:settingswindow
                 visible:false
                 width: 1350
                 height: 800
                 title: qsTr("settings")
                 Rectangle {
                     id: rectangle5sett
                     x: 1
                     y: 0
                     width: 1349
                     height: 46
                     color: "#e1dddd"
                 }
                 Button {
                     id: buttonlogosetting
                     x: 0
                     y: 0
                     width: 82
                     height: 40
                     palette.button:"white"
                     Image {
                         id: idslogosetting
                         x: 0
                         y: -3
                         width:82
                         height:40
                         smooth: true
                         anchors.fill:parent
                         anchors.top: button.top
                         anchors.bottom: innerText.top
                         fillMode: Image.PreserveAspectFit
                         horizontalAlignment: Image.AlignHCenter
                         source: "qrc:/image/idslogo.PNG"
                         anchors.rightMargin: 0
                         anchors.bottomMargin: -2
                         anchors.leftMargin: 0
                         anchors.topMargin: 0
                     }
                     onClicked: {
                         mainwindow.visible=true
                         settingswindow.visible=false
                         buttonacc.visible= false
                         buttonacomp.visible= false
                         buttonalevel.visible= false
                         buttonaCALIB.visible= false
                         buttonsensorsett.visible= false
                     }

                     ComboBox {
                         id: comboBox1sett
                         x: 941
                         y: 12
                         width: 140
                         height: 25
                         displayText: "PORT"
                         font.family: "ARIAL BLACK"
                         font.pointSize: 8
                         background: Rectangle {
                             color:"#FFFFFF"
                         }

                            model: serial.availablePorts()
//                         serialportbt {
//                             id: serial
//                         }

                     }

                     ComboBox {
                         id: comboBox2sett
                         x: 1099
                         y: 12
                         width: 140
                         height: 25
                         displayText: "BAUDRATE"
                         font.family: "ARIAL BLACK"
                         font.pointSize: 8
                      
                         background: Rectangle {
                             color:"#FFFFFF"
                         }
                     }

                     Button {
                         id: button6sett
                         x: 1252
                         y: 4
                         width: 82
                         height: 40


                         Image {
                                id: bgimgsett
                                width:82
                                height:40
                                smooth: true
                                anchors.top: button.top
                                anchors.bottom: innerText.top
                                fillMode: Image.PreserveAspectFit
                                horizontalAlignment: Image.AlignHCenter
                                source: "qrc:/image/connect.png"
                         }
                     }


                     Button {
                         id: buttonsett
                         x: 90
                         y: 4
                         width: 82
                         height: 40

                         Image {
                                id: setingsett
                                width:82
                                height:40
                                smooth: true
                                anchors.top: button.top
                                anchors.bottom: innerText.top
                                fillMode: Image.PreserveAspectFit
                                horizontalAlignment: Image.AlignHCenter
                                source: "qrc:/image/settings.png"
                         }

                     }

                     Button {
                         id: buttonsensor
                         x: 80
                         y: 50
                         width: 150
                         height: 60
                         palette.button: "LIGHT GREY"

                         Text{
                              text: qsTr("SENSORS")
                              color:"BLACK"
                              font.pointSize: 10
                              font.family: "Arial"
                              x:40
                              y:20
                              font.bold: true

                         }
                         onClicked: {
                             buttonacc.visible= true
                             buttonacomp.visible= true
                             buttonalevel.visible= true
                             buttonaCALIB.visible= true
                             buttonsensorsett.visible= true
                         }
                     }

                     Button {
                         id: buttonacc
                         x: 235
                         y: 50
                         width: 150
                         height: 60
                         palette.button: "#355C7D"
                         visible: false

                         Text{
                              text: qsTr("ACCELEROMETER")
                              color:"white"
                              font.pointSize: 10
                              font.family: "Arial"
                              x:10
                              y:20
                               font.bold: true

                         }
                     }
                     Button {
                         id: buttonacomp
                         x: 235
                         y: 115
                         width: 150
                         height: 60
                         palette.button: "#6C5B7B"
                         visible: false

                         Text{
                              text: qsTr("COMPASS")
                              color:"WHITE"
                              font.pointSize: 10
                              font.family: "Arial"
                              x:10
                              y:20
                               font.bold: true

                         }
                     }
                     Button {
                         id: buttonalevel
                         x: 235
                         y: 180
                         width: 150
                         height: 60
                         palette.button: "#C06C84"
                         visible: false
                         Text{
                              text: qsTr("LEVEL HORIZON")
                              color:"WHITE"
                              font.pointSize: 10
                              font.family: "Arial"
                              x:10
                              y:20
                               font.bold: true

                         }
                     }
                     Button {
                         id: buttonaCALIB
                         x: 235
                         y: 245
                         width: 150
                         height: 60
                         palette.button: "#C06C70"
                         visible: false
                         Text{
                              text: qsTr("CALIBRATE PRESSURE")
                              color:"WHITE"
                              font.pointSize: 10
                              font.family: "Arial"
                              x:2
                              y:20
                               font.bold: true

                         }
                     }
                     Button {
                         id: buttonsensorsett
                         x: 235
                         y: 310
                         width: 150
                         height: 60
                         palette.button: "#F8B195"
                         visible: false
                         Text{
                              text: qsTr("SENSOR SETTINGS")
                              color:"WHITE"
                              font.pointSize: 10
                              font.family: "Arial"
                              x:10
                              y:20
                               font.bold: true

                         }
                     }



                   }

            }
        }
        Button {
            id: buttonlogo
            x: -1244
            y: -45
            width: 82
            height: 40
            palette.button:"white"
            Image {
                id: logo
                x: 0
                y: -3
                width:82
                height:40
                smooth: true
                anchors.fill:parent
                anchors.top: button.top
                anchors.bottom: innerText.top
                fillMode: Image.PreserveAspectFit
                horizontalAlignment: Image.AlignHCenter
                source: "qrc:/image/idslogo.PNG"
                anchors.rightMargin: 0
                anchors.bottomMargin: -2
                anchors.leftMargin: 0
                anchors.topMargin: 0
            }

        }





        }

}


/*##^##
Designer {
    D{i:0;formeditorZoom:0.66}
}
##^##*/

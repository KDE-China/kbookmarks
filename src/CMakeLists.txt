# TODO
remove_definitions(-DQT_NO_CAST_FROM_ASCII)

ecm_create_qm_loader(kbookmarks_QM_LOADER kbookmarks5_qt)

set(kbookmarks_SRCS
  kbookmark.cpp
  kbookmarkaction.cpp
  kbookmarkactioninterface.cpp
  kbookmarkactionmenu.cpp
  kbookmarkcontextmenu.cpp
  kbookmarkimporter.cpp
  kbookmarkmanager.cpp
  kbookmarkmanageradaptor.cpp
  kbookmarkmenu.cpp
  kbookmarkowner.cpp
  konqbookmarkmenu.cpp
  kbookmarkimporter_opera.cpp
  kbookmarkimporter_ie.cpp
  kbookmarkimporter_ns.cpp
  kbookmarkdombuilder.cpp
  kbookmarkdialog.cpp
  ${kbookmarks_QM_LOADER}
)


ecm_qt_declare_logging_category(kbookmarks_SRCS HEADER kbookmarks_debug.h IDENTIFIER KBOOKMARKS_LOG CATEGORY_NAME kf5.kbookmarks)

add_library(KF5Bookmarks ${kbookmarks_SRCS})
generate_export_header(KF5Bookmarks BASE_NAME KBookmarks)
add_library(KF5::Bookmarks ALIAS KF5Bookmarks)

target_include_directories(KF5Bookmarks INTERFACE "$<INSTALL_INTERFACE:${KDE_INSTALL_INCLUDEDIR_KF5}/KBookmarks>")

target_link_libraries(KF5Bookmarks PUBLIC Qt5::Widgets Qt5::Xml KF5::WidgetsAddons)
target_link_libraries(KF5Bookmarks PRIVATE
    Qt5::DBus # dbus usage in kbookmarkmanager.cpp
    KF5::CoreAddons # for KStringHandler
    KF5::Codecs # for KCharsets
    KF5::ConfigCore # for KConfigGroup
    KF5::ConfigWidgets # for KStandardAction
    KF5::XmlGui # for KActionCollection
    KF5::IconThemes # for SmallIcon
)

set_target_properties(KF5Bookmarks PROPERTIES VERSION   ${KBOOKMARKS_VERSION_STRING}
                                              SOVERSION ${KBOOKMARKS_SOVERSION}
                                              EXPORT_NAME Bookmarks
)

ecm_generate_headers(KBookmarks_HEADERS
  HEADER_NAMES
  KBookmark
  KBookmarkAction
  KBookmarkActionInterface
  KBookmarkActionMenu
  KBookmarkContextMenu
  KBookmarkManager
  KBookmarkMenu
  KBookmarkOwner
  KBookmarkDomBuilder
  KBookmarkDialog
  KonqBookmarkMenu

  REQUIRED_HEADERS KBookmarks_HEADERS
)

# These classes have weirdly named classes, no forwarding headers.
set(KBookmarks_HEADERS
  ${KBookmarks_HEADERS}
  kbookmarkexporter.h
  kbookmarkimporter.h
  kbookmarkimporter_opera.h
  kbookmarkimporter_ie.h
  kbookmarkimporter_ns.h
)
install(TARGETS KF5Bookmarks EXPORT KF5BookmarksTargets ${KF5_INSTALL_TARGETS_DEFAULT_ARGS})

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/kbookmarks_export.h
  ${KBookmarks_HEADERS}
  DESTINATION ${KDE_INSTALL_INCLUDEDIR_KF5}/KBookmarks COMPONENT Devel
)

if(BUILD_QCH)
    ecm_add_qch(
        KF5Bookmarks_QCH
        NAME KBookmarks
        BASE_NAME KF5Bookmarks
        VERSION ${KF5_VERSION}
        ORG_DOMAIN org.kde
        SOURCES # using only public headers, to cover only public API
            ${KBookmarks_HEADERS}
        MD_MAINPAGE "${CMAKE_SOURCE_DIR}/README.md"
        LINK_QCHS
            Qt5Widgets_QCH
            Qt5Xml_QCH
            KF5WidgetsAddons_QCH
        BLANK_MACROS
            KCOREADDONS_EXPORT
            KCOREADDONS_DEPRECATED
            KCOREADDONS_DEPRECATED_EXPORT
        TAGFILE_INSTALL_DESTINATION ${KDE_INSTALL_QTQCHDIR}
        QCH_INSTALL_DESTINATION ${KDE_INSTALL_QTQCHDIR}
        COMPONENT Devel
    )
endif()

include(ECMGeneratePriFile)
ecm_generate_pri_file(BASE_NAME KBookmarks LIB_NAME KF5Bookmarks DEPS "widgets xml" FILENAME_VAR PRI_FILENAME INCLUDE_INSTALL_DIR ${KDE_INSTALL_INCLUDEDIR_KF5}/KBookmarks)
install(FILES ${PRI_FILENAME} DESTINATION ${ECM_MKSPECS_INSTALL_DIR})

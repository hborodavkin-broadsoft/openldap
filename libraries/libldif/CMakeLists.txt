# libldif is part of openldap

PROJECT(libldif)

SET (INC_DIRS ./
	 ../../include
)

#Windows additional directories
IF(WIN32)
LIST(APPEND INC_DIRS ../../include/win32)
ELSEIF(UNIX AND NOT APPLE)
LIST(APPEND INC_DIRS ../../include/linux)
ELSEIF(APPLE)
LIST(APPEND INC_DIRS ../../include/mac)
ENDIF(WIN32)
#ADD_DEFINITIONS( -DLDAP_LIBS_DYNAMIC )
INCLUDE_DIRECTORIES(
${INC_DIRS}
)
SET(HEADERS ../../include/ldif.h)
SET(SOURCES fetch.c  line64.c)

ADD_LIBRARY(libldif STATIC
            ${HEADERS}
            ${SOURCES}
)

# Dot parser
# Create custom command for flex/lex
find_package(BISON REQUIRED)
find_package(FLEX REQUIRED)

if (BISON_FOUND)

  # Creation of the Parser
  BISON_TARGET(Parser
    Parser.y
    ${CMAKE_CURRENT_BINARY_DIR}/Parser.cpp
    DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/Parser.hpp
    COMPILE_FLAGS "-y"
    )

  if(FLEX_FOUND)

    # Creation of the Lexer
    FLEX_TARGET(Scanner
      Lexer.l
      ${CMAKE_CURRENT_BINARY_DIR}/Lexer.cpp
      COMPILE_FLAGS "--header-file=${CMAKE_CURRENT_BINARY_DIR}/Lexer.hpp"
      )

    ADD_FLEX_BISON_DEPENDENCY(Scanner Parser)
  else()
    message("[-] Flex issues")
  endif()
else()
  message("[-] Bison issues")
endif()


# sources
set(SRCS_DOTPARSER
  graphParser.cpp
  graphParser.hpp
  Expression.cpp
  Expression.hpp
  )

set(SRCS_BISON
  ${FLEX_Scanner_OUTPUTS}
  ${BISON_Parser_OUTPUTS}
  )

# Removed warnings for the parser and lexer
SET_SOURCE_FILES_PROPERTIES(${SRCS_BISON} PROPERTIES
  COMPILE_FLAGS "-w"
  GENERATED TRUE)

# Bindings
list_combinations(SRCS_DOTPARSER_BINDING
  PREFIXES ${CMAKE_CURRENT_SOURCE_DIR}/
  SUFFIXES ${SRCS_DOTPARSER})

binding_add_srcs("${SRCS_DOTPARSER_BINDING}")
binding_add_srcs("${SRCS_BISON}")

# dotparser object-library
add_library(dotparser-obj OBJECT ${SRCS_DOTPARSER} ${SRCS_BISON})


# Copyright (c) 2014-present, Facebook, Inc.
# All rights reserved.
#
# This source code is licensed in accordance with the terms specified in
# the LICENSE file found in the root directory of this source tree.

function(osqueryTablesSystemTestsMain)

  if(DEFINED PLATFORM_LINUX)
    generateOsqueryTablesSystemLinuxTests()
  endif()

  if(DEFINED PLATFORM_POSIX)
    generateOsqueryTablesSystemPosixTests()
    generateOsqueryTablesSystemTestsAugeasTestsTest()
  endif()

  generateOsqueryTablesSystemTestsSystemtablestestsTest()

  if(DEFINED PLATFORM_MACOS)
    generateOsqueryTablesSystemDarwinTests()
  endif()

  if(DEFINED PLATFORM_WINDOWS)
    generateOsqueryTablesSystemWindowsTests()
  endif()
endfunction()

function(generateOsqueryTablesSystemLinuxTests)
  add_osquery_executable(osquery_tables_system_linux_tests-test
    linux/md_tables_tests.cpp
    linux/pci_devices_tests.cpp
    linux/pcidb_tests.cpp
    linux/portage_tests.cpp
  )

  target_link_libraries(osquery_tables_system_linux_tests-test PRIVATE
    osquery_cxx_settings
    osquery_config_tests_testutils
    osquery_core
    osquery_core_sql
    osquery_database
    osquery_filesystem
    osquery_remote_tests_remotetestutils
    osquery_tables_system_systemtable
    osquery_utils
    osquery_utils_conversions
    plugins_database_ephemeral
    thirdparty_googletest
  )
endfunction()

function(generateOsqueryTablesSystemPosixTests)
  add_osquery_executable(osquery_tables_system_posix_tests-test
    posix/apt_sources_tests.cpp
    posix/known_hosts_tests.cpp
    posix/shell_history_tests.cpp
    posix/sudoers_tests.cpp
    posix/yum_sources_tests.cpp
  )

  target_link_libraries(osquery_tables_system_posix_tests-test PRIVATE
    osquery_cxx_settings
    osquery_config_tests_testutils
    osquery_core
    osquery_core_sql
    osquery_database
    osquery_filesystem
    osquery_remote_tests_remotetestutils
    osquery_tables_system_systemtable
    osquery_utils
    osquery_utils_conversions
    plugins_database_ephemeral
    thirdparty_googletest
  )
endfunction()

function(generateOsqueryTablesSystemTestsSystemtablestestsTest)
  add_osquery_executable(osquery_tables_system_tests_systemtablestests-test
    system_tables_tests.cpp
  )

  target_link_libraries(osquery_tables_system_tests_systemtablestests-test PRIVATE
    osquery_cxx_settings
    osquery_config_tests_testutils
    osquery_core
    osquery_core_sql
    osquery_database
    osquery_filesystem
    osquery_remote_tests_remotetestutils
    osquery_tables_system_systemtable
    osquery_utils
    osquery_utils_conversions
    plugins_database_ephemeral
    thirdparty_googletest
  )
endfunction()

function(generateOsqueryTablesSystemDarwinTests)
  add_osquery_executable(osquery_tables_system_darwin_tests-test
    darwin/apps_tests.cpp
    darwin/asl_tests.cpp
    darwin/certificates_tests.cpp
    darwin/extended_attributes_tests.cpp
    darwin/firewall_tests.cpp
    darwin/launchd_tests.cpp
    darwin/mdfind_tests.cpp
    darwin/processes_tests.cpp
    darwin/smc_tests.cpp
    darwin/startup_items_tests.cpp
    darwin/signature_tests.mm
  )

  target_link_libraries(osquery_tables_system_darwin_tests-test PRIVATE
    osquery_cxx_settings
    osquery_config_tests_testutils
    osquery_core
    osquery_core_sql
    osquery_database
    osquery_filesystem
    osquery_remote_tests_remotetestutils
    osquery_tables_system_systemtable
    osquery_utils
    osquery_utils_conversions
    plugins_database_ephemeral
    thirdparty_googletest
    osquery_tools_tests_plistfiles
    osquery_tools_tests_testfiles
  )
endfunction()

function(generateOsqueryTablesSystemWindowsTests)
  add_osquery_executable(osquery_tables_system_windows_tests-test
    windows/certificates_tests.cpp
    windows/registry_tests.cpp
  )

  target_link_libraries(osquery_tables_system_windows_tests-test PRIVATE
    osquery_cxx_settings
    osquery_config_tests_testutils
    osquery_core
    osquery_core_sql
    osquery_database
    osquery_filesystem
    osquery_remote_tests_remotetestutils
    osquery_tables_system_systemtable
    osquery_utils
    osquery_utils_conversions
    plugins_database_ephemeral
    thirdparty_googletest
    osquery_tools_tests_testfiles
  )
endfunction()

function(generateOsqueryTablesSystemTestsAugeasTestsTest)
  add_osquery_executable(osquery_tables_system_tests_augeastests-test
    posix/augeas_tests.cpp
  )

  target_link_libraries(osquery_tables_system_tests_augeastests-test PRIVATE
    osquery_cxx_settings
    osquery_config_tests_testutils
    osquery_dispatcher
    osquery_events
    osquery_extensions
    osquery_extensions_implthrift
    osquery_headers
    osquery_registry
    osquery_remote_enroll_tlsenroll
    osquery_sql
    osquery_tables_events_eventstable
    plugins_database_ephemeral
    specs_tables
    thirdparty_googletest
  )

  get_property(augeas_lenses_path GLOBAL PROPERTY AUGEAS_LENSES_FOLDER_PATH)

  add_custom_target(osquery_tables_system_tests_augeaslenses
    COMMAND "${CMAKE_COMMAND}" -E make_directory "${TEST_CONFIGS_DIR}/augeas/lenses"
    COMMAND "${CMAKE_COMMAND}" -E copy "${augeas_lenses_path}/*.aug" "${TEST_CONFIGS_DIR}/augeas/lenses")

  add_dependencies(osquery_tables_system_tests_augeastests-test osquery_tables_system_tests_augeaslenses)
  add_dependencies(osquery_tables_system_tests_augeaslenses thirdparty_augeas)
  add_dependencies(osquery_tables_system_tests_augeaslenses osquery_tools_tests_testconfigsfolder)

endfunction()

osqueryTablesSystemTestsMain()

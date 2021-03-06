#==========================================================================
#  AIDA Detector description implementation 
#--------------------------------------------------------------------------
# Copyright (C) Organisation europeenne pour la Recherche nucleaire (CERN)
# All rights reserved.
#
# For the licensing terms see $DD4hepINSTALL/LICENSE.
# For the list of contributors see $DD4hepINSTALL/doc/CREDITS.
#
#==========================================================================
#
# DDDB is a detector description convention developed by the LHCb experiment.
# For further information concerning the DTD, please see:
# http://lhcb-comp.web.cern.ch/lhcb-comp/Frameworks/DetDesc/Documents/lhcbDtd.pdf
#
# The usage of Xerces-C is mandatory due to the entity resolution mechanism
#

#==========================================================================
cmake_minimum_required(VERSION 3.3 FATAL_ERROR)

if (DD4HEP_USE_XERCESC)
  include ( ${DD4hep_DIR}/cmake/DD4hep.cmake )

  #------------------------------------------------------------------------------
  dd4hep_configure_output ()
  dd4hep_package (DDDB MAJOR 0 MINOR 0 PATCH 1
    USES  [XERCESC REQUIRED]
          [ROOT    REQUIRED COMPONENTS Geom] 
          [DD4hep  REQUIRED COMPONENTS DDCore DDDB]
    )
  #---Package installation procedure(s) -----------------------------------------
  install ( PROGRAMS scripts/run_dddb.sh DESTINATION bin)
  install ( PROGRAMS scripts/display_dddb.sh DESTINATION bin)
  install ( PROGRAMS scripts/extract_dddb.sh DESTINATION bin)
  install ( FILES    data/DDDB.tar.gz DESTINATION examples/DDDB)
  #---Testing--------------------------------------------------------------------
  dd4hep_configure_scripts ( DDDB DEFAULT_SETUP WITH_TESTS )
  #
  #---Testing: Extract DDDB data from zip archive -------------------------------
  dd4hep_add_test_reg( DDDB_extract_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/extract_dddb.sh
    REGEX_PASS "DDDB Database successfully installed."
  )
  #
  #---Testing: Load the geometry from archive -----------------------------------
  dd4hep_add_test_reg( DDDB_load_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config DD4hep_ConditionsManagerInstaller
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ Converted    12852 placements"
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + conditions from archive ----------------------
  dd4hep_add_test_reg( DDDB_conditions_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ Converted     9353 conditions" 
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + conditions dump as view from DetElement ------
  dd4hep_add_test_reg( DDDB_conditions_dump_simple_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -exec      DDDB_ConditionsSummary
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB Total Number of parameters:    13642  " 
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + dump detector elemets ------------------------
  dd4hep_add_test_reg( DDDB_det_elements_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller  -end-plugin
    -plugin    DDDB_DetectorDump -print DEBUG   -end-plugin
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Number of DetElements in the geometry:     6261" 
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + dump volumes ---------------------------------
  dd4hep_add_test_reg( DDDB_det_volumes_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DetectorVolumeDump -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Number of DetElement placements:           6253" 
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + dump condition keys --------------------------
  dd4hep_add_test_reg( DDDB_det_conditions_keys_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DetectorConditionKeysDump -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Number of DetElement condition keys:       9043"
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + dump condition keys --------------------------
  dd4hep_add_test_reg( DDDB_det_catalog_data_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DetectorConditionDump -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDBConditions Total Number of parameters:   346312 "
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + dump condition keys --------------------------
  dd4hep_add_test_reg( DDDB_det_catalog_align_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DetectorAlignmentDump -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Number of attached alignments:             5408"
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + conditions dump as view from DetElement ------
  dd4hep_add_test_reg( DDDB_detelement_conditions_dump_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DetElementConditionDump -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Total number of DetElement parameters:    13136"
  )
  #
  #---Testing: Load the geometry + conditions + conditions derives
  dd4hep_add_test_reg( DDDB_derived_conditions_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DerivedCondTest -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Total Number of callbacks:    16221"
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + conditions + run basic derived alignments test
  dd4hep_add_test_reg( DDDB_alignment_derived_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_DerivedAlignmentsTest -print DEBUG -turns 1 -access 3
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: AlignmentManager:    9352 conditions \\(S:0,L:9352,C:0,M:0\\) alignments: \\(A:6200,M:0\\) for IOV:epoch\\(0\\)" 
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Load the geometry + conditions + access derived alignments from DetElement structures
  dd4hep_add_test_reg( DDDB_alignment_access_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/run_dddb.sh
    -config    DD4hep_ConditionsManagerInstaller
    -plugin    DDDB_AlignmentsAccessTest -print DEBUG
    DEPENDS    DDDB_extract_LONGTEST
    REGEX_PASS "\\+ DDDB: Accessed 5407 alignments from 5407 DetElement objects" 
    REGEX_FAIL "EXCEPTION;Exception"
  )
  #
  #---Testing: Extract DDDB data from zip archive -------------------------------
  dd4hep_add_test_reg( DDDB_clean_LONGTEST
    COMMAND    "${CMAKE_INSTALL_PREFIX}/bin/run_test_DDDB.sh"
    EXEC_ARGS  ${CMAKE_INSTALL_PREFIX}/bin/extract_dddb.sh -clean
    DEPENDS    DDDB_extract_LONGTEST
    DDDB_alignment_access_LONGTEST
    DDDB_alignment_derived_LONGTEST
    DDDB_derived_conditions_LONGTEST
    DDDB_detelement_conditions_dump_LONGTEST
    DDDB_det_catalog_align_LONGTEST
    DDDB_det_catalog_data_LONGTEST
    DDDB_det_conditions_keys_LONGTEST
    DDDB_det_volumes_LONGTEST
    DDDB_det_elements_LONGTEST
    DDDB_conditions_dump_simple_LONGTEST
    DDDB_conditions_LONGTEST
    DDDB_load_LONGTEST
    DDDB_extract_LONGTEST
    REGEX_PASS "DDDB Database successfully removed" )

endif()


macro(shared_build_pass Source Message)
    build_pass("libshared.h" "libshared" "libshared" "${Source}" ${Message})
endmacro()

macro(shared_build_fail Source Message)
    build_fail("libshared.h" "libshared" "libshared" "${Source}" ${Message})
endmacro()

shared_build_pass("Libshared l; return l.libshared_exported();" "Failed to build exported")

# if (COMPILER_HAS_DEPRECATED)
#   shared_build_fail("Libshared l; return l.libshared_deprecated();" "Built use of deprecated class method. This should not be possible.")
# else()
#   shared_build_pass("Libshared l; return l.libshared_deprecated();" "Built use of deprecated class method. This should not be possible.")
# endif()
if (COMPILER_HAS_HIDDEN_VISIBILITY)
  shared_build_fail("Libshared l; return l.libshared_excluded();" "Built use of excluded class method. This should not be possible.")
else()
  # There is no MSVC equivalent to hiding symbols.
  shared_build_pass("Libshared l; return l.libshared_excluded();" "Built use of excluded class method. This is possible on MSVC.")
endif()

if (WIN32 OR COMPILER_HAS_HIDDEN_VISIBILITY)
  shared_build_fail("LibsharedNotExported l; return l.libshared();" "Built use of not-exported class method. This should not be possible.")
  shared_build_fail("LibsharedNotExported l; return l.libshared_not_exported();" "Built use of not-exported class method. This should not be possible.")
  shared_build_fail("LibsharedNotExported l; return l.libshared_excluded();" "Built use of not-exported class method. This should not be possible.")
  shared_build_fail("LibsharedExcluded l; return l.libshared();" "Built use of excluded class method. This should not be possible.")
  shared_build_fail("LibsharedExcluded l; return l.libshared_not_exported();" "Built use of excluded class method. This should not be possible.")
  shared_build_fail("LibsharedExcluded l; return l.libshared_excluded();" "Built use of excluded class method. This should not be possible.")

  shared_build_fail("return libshared_excluded();" "Built use of excluded function. This should not be possible.")
  shared_build_fail("return libshared_not_exported();" "Built use of not-exported function. This should not be possible.")
else()
  shared_build_pass("LibsharedNotExported l; return l.libshared();" "Built use of not-exported class method.")
  shared_build_pass("LibsharedNotExported l; return l.libshared_not_exported();" "Built use of not-exported class method.")
  shared_build_pass("LibsharedNotExported l; return l.libshared_excluded();" "Built use of not-exported class method.")
  shared_build_pass("LibsharedExcluded l; return l.libshared();" "Built use of excluded class method.")
  shared_build_pass("LibsharedExcluded l; return l.libshared_not_exported();" "Built use of excluded class method.")
  shared_build_pass("LibsharedExcluded l; return l.libshared_excluded();" "Built use of excluded class method.")

  shared_build_pass("return libshared_excluded();" "Built use of excluded function.")
  shared_build_pass("return libshared_not_exported();" "Built use of not-exported function.")
endif()

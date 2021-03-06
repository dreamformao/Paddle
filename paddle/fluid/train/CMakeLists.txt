function(train_test TARGET_NAME)
    set(options "")
    set(oneValueArgs "")
    set(multiValueArgs ARGS)
    cmake_parse_arguments(train_test "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

    set(arg_list "")
    if(train_test_ARGS)
        foreach(arg ${train_test_ARGS})
            list(APPEND arg_list "_${arg}")
        endforeach()
    else()
        list(APPEND arg_list "_")
    endif()
    foreach(arg ${arg_list})
        string(REGEX REPLACE "^_$" "" arg "${arg}")
        if (NOT APPLE AND NOT WIN32)
            cc_test(test_train_${TARGET_NAME}${arg}
                    SRCS test_train_${TARGET_NAME}.cc
                    DEPS paddle_fluid_shared
                    ARGS --dirname=${PYTHON_TESTS_DIR}/book/${TARGET_NAME}${arg}.train.model/)
        else()
            cc_test(test_train_${TARGET_NAME}${arg}
                    SRCS test_train_${TARGET_NAME}.cc
                    DEPS paddle_fluid_api
                    ARGS --dirname=${PYTHON_TESTS_DIR}/book/${TARGET_NAME}${arg}.train.model/)
        endif()
        set_tests_properties(test_train_${TARGET_NAME}${arg}
                PROPERTIES DEPENDS test_${TARGET_NAME})
        set_tests_properties(test_train_${TARGET_NAME}${arg}
                PROPERTIES LABELS "RUN_TYPE=DIST")
    endforeach()
endfunction(train_test)


if(WITH_TESTING)
  train_test(recognize_digits ARGS mlp conv)
endif()

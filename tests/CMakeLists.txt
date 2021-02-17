# Define the test binary
add_executable(
        ${PROJECT_NAME}-test
        test.cpp)

target_link_libraries(${PROJECT_NAME}-test
        PRIVATE
        ${PROJECT_NAME})

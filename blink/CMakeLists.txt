cmake_minimum_required(VERSION 3.16)

# Specify the target architecture
set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_VERSION 1)
set(CMAKE_SYSTEM_PROCESSOR cortex-m4)

# Compiler options
set(CMAKE_C_COMPILER arm-none-eabi-gcc)
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)
set(CMAKE_ASM_COMPILER arm-none-eabi-gcc)
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)

# Force test to build static library
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)

# Project name and language
project(blink C CXX ASM)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_STANDARD 99)

set(MCPU cortex-m4)

# Define your custom linker script
set(LINKER_SCRIPT "${CMAKE_SOURCE_DIR}/STM32F407VETX_FLASH.ld")

# Define include directories
set(INCLUDE_DIRS
    ${CMAKE_SOURCE_DIR}/include
#   ${CMAKE_SOURCE_DIR}/drivers
#   ${CMAKE_SOURCE_DIR}/cmsis
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/CMSIS/STM32F4xx/Include
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/STM32F4xx_HAL_Driver/Inc
)

# Define source files
file(GLOB_RECURSE SOURCES
    ${CMAKE_SOURCE_DIR}/src/*.c
    ${CMAKE_SOURCE_DIR}/src/*.cpp
    ${CMAKE_SOURCE_DIR}/src/*.S
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/STM32F4xx_HAL_Driver/Src/*.c
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/STM32F4xx_HAL_Driver/Src/*.S
)
# Add system_stm32f4xx.c and startup_stm32f407xx.s explicitly
list(APPEND SOURCES
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/CMSIS/STM32F4xx/Source/Templates/system_stm32f4xx.c
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/CMSIS/STM32F4xx/Source/Templates/gcc/startup_stm32f407xx.s
)

# Filter out files containing "template"
list(FILTER SOURCES EXCLUDE REGEX ".*template.*")

# Define compiler flags
#set(CMAKE_C_FLAGS "-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard -Wall -Werror -O2")
set(CMAKE_CXX_FLAGS "-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard -Wall -Werror -O2 -fno-exceptions -fno-rtti")
set(CMAKE_ASM_FLAGS "-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard")

# Define linker flags
set(LINKER_FLAGS "-T${LINKER_SCRIPT} -mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard -Wl,--gc-sections -Wl,-Map=${CMAKE_BINARY_DIR}/output.map -specs=nosys.specs")

# Add executable target
add_executable(${PROJECT_NAME}.elf ${SOURCES})

# Include directories
target_include_directories(${PROJECT_NAME}.elf PRIVATE 
    ${CMAKE_SOURCE_DIR}/include
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/CMSIS/STM32F4xx/Include
    ${CMAKE_SOURCE_DIR}/../lib/stm32lib/STM32F4xx_HAL_Driver/Inc
    ${CMAKE_SOURCE_DIR}/../lib/cmsis/inc
)

# Apply compiler flags
#target_compile_options(${PROJECT_NAME}.elf PRIVATE ${CMAKE_CXX_FLAGS})

# Apply linker flags
#target_link_options(${PROJECT_NAME}.elf PRIVATE ${LINKER_FLAGS})

# Apply compiler flags
target_compile_options(${PROJECT_NAME}.elf PRIVATE
    -mcpu=cortex-m4
    -mthumb
    -mthumb-interwork
    -ffunction-sections
    -fdata-sections
    -fno-common
    -fmessage-length=0
    -DSTM32F407xx
)

# Apply linker flags
target_link_options(${PROJECT_NAME}.elf PRIVATE
    -T${LINKER_SCRIPT}
    -mcpu=cortex-m4
    -mthumb
    -mthumb-interwork
    -Wl,--gc-sections
    -Wl,--print-memory-usage
    -Wl,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map
)


# Optional: Create binary and hex files
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} -O binary ${PROJECT_NAME}.elf ${PROJECT_NAME}.bin
    COMMAND ${CMAKE_OBJCOPY} -O ihex ${PROJECT_NAME}.elf ${PROJECT_NAME}.hex
)

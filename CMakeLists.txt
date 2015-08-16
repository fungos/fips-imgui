#
# project: fips-imgui
#

if (NOT FIPS_IMPORT)
    cmake_minimum_required(VERSION 2.8)
    get_filename_component(FIPS_ROOT_DIR "../fips" ABSOLUTE)
    include("${FIPS_ROOT_DIR}/cmake/fips.cmake")
    fips_setup()
    fips_project(fips-imgui)
endif()

fips_begin_lib(imgui)
    fips_vs_disable_warnings(4312)   # 'type cast': conversion from 'int' to 'void *' of greater size
    include_directories(imgui)
    fips_dir(imgui)
    fips_files(
        imconfig.h
        imgui.cpp
        imgui.h
        imgui_demo.cpp
        imgui_draw.cpp
        imgui_internal.h
        stb_rect_pack.h
        stb_textedit.h
        stb_truetype.h
    )
fips_end_lib()

if (NOT FIPS_IMPORT)
    if (FIPS_MACOS OR FIPS_LINUX OR FIPS_WINDOWS)
        fips_begin_app(opengl_example cmdline)
            include_directories(imgui/examples/opengl_example)
            fips_dir(imgui/examples/opengl_example)
            fips_files(main.cpp imgui_impl_glfw.h imgui_impl_glfw.cpp)
            fips_deps(imgui glfw3)

            if (FIPS_WINDOWS)
                fips_libs(opengl32)
            endif()

            if (FIPS_LINUX)
                fips_libs(X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL m)
            endif()

            if (FIPS_MACOS)
                fips_frameworks_osx(Cocoa CoreVideo OpenGL)
                fips_libs(m)
            endif()
        fips_end_app()
        fips_begin_app(opengl3_example cmdline)
            include_directories(imgui/examples/libs/gl3w)
            include_directories(imgui/examples/opengl3_example)
            fips_dir(imgui/examples/libs/gl3w/GL)
            fips_files(gl3w.c gl3w.h glcorearb.h)
            fips_dir(imgui/examples/opengl3_example)
            fips_files(main.cpp imgui_impl_glfw_gl3.h imgui_impl_glfw_gl3.cpp)
            fips_deps(imgui glfw3)

            if (FIPS_WINDOWS)
                fips_libs(opengl32)
            endif()

            if (FIPS_LINUX)
                fips_libs(X11 Xrandr Xi Xinerama Xxf86vm Xcursor GL m dl)
            endif()

            if (FIPS_MACOS)
                fips_frameworks_osx(Cocoa CoreVideo OpenGL)
                fips_libs(m dl)
            endif()
        fips_end_app()
    endif()

    if (FIPS_WINDOWS)
        # fips_begin_app(directx9_example cmdline)
            # include_directories(imgui/examples/directx9_example "$ENV{DXSDK_DIR}Include")
            # fips_dir(imgui/examples/directx9_example)
            # fips_files(main.cpp imgui_impl_dx9.h imgui_impl_dx9.cpp)
            # fips_deps(imgui)
            # fips_libs(d3d9 d3dx9d dxee dxguid)
        # fips_end_app()
        fips_begin_app(directx11_example cmdline)
			add_definitions(-DUNICODE)
            include_directories(imgui/examples/directx11_example)
            fips_dir(imgui/examples/directx11_example)
            fips_files(main.cpp imgui_impl_dx11.h imgui_impl_dx11.cpp)
            fips_deps(imgui)
            fips_libs(d3d11 d3dcompiler dxgi imm32)
        fips_end_app()
    endif()
endif()

if (FIPS_CLANG)
    set_target_properties(imgui PROPERTIES COMPILE_FLAGS "-Wno-unused-but-set-variable -Wno-type-limits -Wno-missing-field-initializers")
elseif(FIPS_GCC)
    set_target_properties(imgui PROPERTIES COMPILE_FLAGS "-Wno-unused-but-set-variable -Wno-type-limits -Wno-missing-field-initializers")
endif()

if (NOT FIPS_IMPORT)
    fips_finish()
endif()


# CMake の最小バージョン指定（3.12 以上で target_compile_options などが安定）
cmake_minimum_required(VERSION 3.12)

# このディレクトリはホスト向けツール（elf2uf2）専用のプロジェクト
project(elf2uf2_tool CXX)

# ビルド開始メッセージ（ログに表示される）
message(STATUS "✅ tool/CMakeLists.txt: Building elf2uf2 from main.cpp")

# elf2uf2 実行ファイルを生成（main.cpp がエントリポイント）
add_executable(elf2uf2 main.cpp)

# ヘッダファイル（elf.h, uf2.h）が tool/ ディレクトリ内にある場合のインクルード設定
target_include_directories(elf2uf2 PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

# C++ の静的デストラクタ登録を無効化（組み込み環境との互換性向上）
target_compile_options(elf2uf2 PRIVATE -fno-use-cxa-atexit)
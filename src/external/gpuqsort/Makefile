LIB_SOURCES 	= gpuqsort.cu
TEST_SOURCES 	= main.cpp dists.cpp
LIB_OBJ		= $(LIB_SOURCES:.cu=.o)
TEST_OBJ	= $(TEST_SOURCES:.cpp=.o)

# building with clang
CUDA_DIR = /usr/local/cuda-10.1

ARCH=sm_61
LLVM_HOME=/home/charitha/workspace/llvm-project-rocm/build_install
CLANG = $(LLVM_HOME)/bin/clang++
OPT = $(LLVM_HOME)/bin/opt
CLANG_FLAGS := -I$(CUDA_DIR)/include --cuda-path=$(CUDA_DIR) --cuda-gpu-arch=$(ARCH) 
OPTLEVEL := -O3
INCLUDES := -Iinc


all: gpuqsortlib testbench

gpuqsortlib: $(LIB_OBJ)
	mkdir -p lib/linux/static
	mkdir -p lib/linux/shared
	nvcc -o lib/linux/static/libgpuqsort.a -lib $(LIB_OBJ)
	nvcc -o lib/linux/shared/libgpuqsort.so -shared $(LIB_OBJ)

testbench: $(TEST_OBJ) gpuqsortlib
	mkdir -p bin/linux
	nvcc -o bin/linux/testbench $(TEST_OBJ) -lgpuqsort -lglut -L./lib/linux/static

%.o: src/%.cu
	nvcc -O3 -I$(CUDA_SDK_DIR)/common/inc -Xcompiler -fPIC -Iinc -c $< -o $@

%.o: testbench/%.cpp
	gcc -O3 -Iinc -c $< -o $@

gpuqsort_clang_cfm.o : src/gpuqsort.cu
	mkdir -p tmp
	sh compile_with_cfmerger.sh

gpuqsort_clang.o : src/gpuqsort.cu
	$(EXEC) $(CLANG) $(INCLUDES) $(CLANG_FLAGS) $(OPTLEVEL) -o $@ -c  $<

testbench_clang : gpuqsort_clang.o $(TEST_OBJ)
	$(EXEC) $(CLANG) -o $@ $^ -lglut -lcudart -L$(CUDA_DIR)/lib64



testbench_clang_cfm : gpuqsort_clang_cfm.o $(TEST_OBJ)
	$(EXEC) $(CLANG) -o $@ $^ -lglut -lcudart -L$(CUDA_DIR)/lib64

clean :
	rm -rf *.o ./bin/* ./lib/* ./*.ll ./tmp/* testbench_clang_cfm testbench_clang

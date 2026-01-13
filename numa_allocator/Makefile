# NUMA-Aware Memory Allocator Makefile

CC = gcc
CFLAGS = -Wall -Wextra -O2 -g -pthread
LDFLAGS = -pthread -lnuma

# Source files
ALLOCATOR_SRC = numa_alloc.c
BENCHMARK_SRC = benchmark.c
VALIDATE_SRC = validate.c

# Object files
ALLOCATOR_OBJ = numa_alloc.o

# Executables
BENCHMARK = benchmark
VALIDATE = validate
QUICK_TEST = quick_test
SHARED_LIB = libnuma_alloc.so

# Default target
all: $(BENCHMARK) $(VALIDATE) $(QUICK_TEST) $(SHARED_LIB)

# Compile allocator object file
$(ALLOCATOR_OBJ): $(ALLOCATOR_SRC) numa_alloc.h
	$(CC) $(CFLAGS) -fPIC -c $(ALLOCATOR_SRC) -o $(ALLOCATOR_OBJ)

# Build shared library
$(SHARED_LIB): $(ALLOCATOR_OBJ)
	$(CC) -shared -o $(SHARED_LIB) $(ALLOCATOR_OBJ) $(LDFLAGS)

# Build benchmark
$(BENCHMARK): $(BENCHMARK_SRC) $(ALLOCATOR_OBJ) numa_alloc.h
	$(CC) $(CFLAGS) $(BENCHMARK_SRC) $(ALLOCATOR_OBJ) -o $(BENCHMARK) $(LDFLAGS)

# Build validation tests
$(VALIDATE): $(VALIDATE_SRC) $(ALLOCATOR_OBJ) numa_alloc.h
	$(CC) $(CFLAGS) $(VALIDATE_SRC) $(ALLOCATOR_OBJ) -o $(VALIDATE) $(LDFLAGS)

# Build quick test
$(QUICK_TEST): quick_test.c $(ALLOCATOR_OBJ) numa_alloc.h
	$(CC) $(CFLAGS) quick_test.c $(ALLOCATOR_OBJ) -o $(QUICK_TEST) $(LDFLAGS)

# Run validation tests
test: $(VALIDATE)
	@echo "=== Running validation tests ==="
	./$(VALIDATE)

# Run quick test
quick: $(QUICK_TEST)
	@echo "=== Running quick test ==="
	./$(QUICK_TEST)

# Run benchmarks
bench: $(BENCHMARK)
	@echo "=== Running benchmarks ==="
	@echo "Note: Run with 'sudo' for huge page support"
	./$(BENCHMARK)

# Clean build artifacts
clean:
	rm -f $(ALLOCATOR_OBJ) $(BENCHMARK) $(VALIDATE) $(QUICK_TEST) $(SHARED_LIB)

# Install shared library (requires root)
install: $(SHARED_LIB)
	@echo "Installing shared library to /usr/local/lib"
	@sudo cp $(SHARED_LIB) /usr/local/lib/
	@sudo ldconfig
	@echo "Installing header to /usr/local/include"
	@sudo cp numa_alloc.h /usr/local/include/

# Uninstall
uninstall:
	@echo "Removing installed files"
	@sudo rm -f /usr/local/lib/$(SHARED_LIB)
	@sudo rm -f /usr/local/include/numa_alloc.h
	@sudo ldconfig

# Help
help:
	@echo "NUMA-Aware Memory Allocator Build System"
	@echo ""
	@echo "Targets:"
	@echo "  all        - Build all executables and shared library (default)"
	@echo "  test       - Run validation tests"
	@echo "  bench      - Run performance benchmarks"
	@echo "  clean      - Remove build artifacts"
	@echo "  install    - Install shared library system-wide (requires sudo)"
	@echo "  uninstall  - Remove installed files (requires sudo)"
	@echo "  help       - Show this message"
	@echo ""
	@echo "Requirements:"
	@echo "  - libnuma-dev package installed"
	@echo "  - gcc compiler"
	@echo "  - pthread support"
	@echo ""
	@echo "Notes:"
	@echo "  - For huge page support, run benchmarks with sudo"
	@echo "  - Configure huge pages: sudo sysctl vm.nr_hugepages=128"

.PHONY: all test bench clean install uninstall help

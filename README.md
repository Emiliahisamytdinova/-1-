#!/bin/bash

# Проверяем, что переданы оба аргумента
if [ $# -ne 2 ]; then
    echo "Usage: $0 <input_directory> <output_directory>"
    exit 1
fi

input_dir="$1"
output_dir="$2"

# Проверяем, существует ли входная директория
if [ ! -d "$input_dir" ]; then
    echo "Input directory '$input_dir' not found."
    exit 1
fi

# Создаем выходную директорию, если она не существует
mkdir -p "$output_dir"

# Используем find для обхода входной директории и копирования файлов в выходную
find "$input_dir" -type f -exec sh -c '
    for filepath; do
        filename=$(basename "$filepath")
        base="${filename%.*}"
        ext="${filename##*.}"
        count=1
        new_filename="$output_dir/$base.$ext"
        while [ -e "$new_filename" ]; do
            new_filename="$output_dir/${base}_$((count++)).$ext"
        done
        cp "$filepath" "$new_filename"
    done
' sh {} +

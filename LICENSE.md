import os
def read_dictionary_from_file(file_path):
    dictionary = {}
    with open(file_path, 'r', encoding='utf-8') as dict_file:
        lines = dict_file.readlines()
        for line in lines:
            try:
                key,value  = line.strip().split('=')
                if key in dictionary:
                    dictionary[key].append(value[1:-1])
                else:
                    dictionary[key]=[value[1:-1]]
            except ValueError:
                print(f"Error in dictionary file: {line.strip()}")
    return dictionary
def has_matching_value(value, dictionary):
    for key in dictionary:
        if all(fkgr_val in value for fkgr_val in key.split('/')):
            return dictionary[key]
    return None

def process_input_files(input_directory, output_directory, dictionary):
    input_files = [f for f in os.listdir(input_directory) if f.endswith('.txt')]

    if not os.path.exists(output_directory):
        os.makedirs(output_directory)

    for input_file in input_files:
        input_file_path = os.path.join(input_directory, input_file)

        with open(input_file_path, 'r', encoding='utf-8') as file:
            output_text = file.read()

        lines = output_text.split('\n')
        output_lines = []
        for line in lines:
            fkgr_values = [val.split(':')[1] for val in line.split() if val.startswith("fkgr:")]

            if fkgr_values:
                combined_fkgr = " ".join(fkgr_values)
                relation_value = has_matching_value(combined_fkgr, dictionary)
                if relation_value:
                    output_lines.append(f'{line} fkgo:{relation_value}')

        output_file_name = os.path.splitext(input_file)[0] + ".txt"
        output_file_path = os.path.join(output_directory, output_file_name)

        with open(output_file_path, 'w', encoding='utf-8') as output_file:
            for output_line in output_lines:
                output_file.write(output_line + '\n')
def find_error_line(file_path):
    with open(file_path, 'r', encoding='utf-8') as dict_file:
        lines = dict_file.readlines()
        for index, line in enumerate(lines, start=1):
            try:
                values, key = line.strip().split('=')
            except ValueError:
                print(f"Error in line {index}: {line.strip()}")
#پوشه ورودی
input_directory =r''
#پوشه خروجی
output_directory = r''
#فایل دیکشنری
dictionary_file_path = r''

dictionary = read_dictionary_from_file(dictionary_file_path)
process_input_files(input_directory, output_directory, dictionary)
find_error_line(dictionary_file_path)

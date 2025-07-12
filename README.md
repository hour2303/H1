import os
import subprocess
import shutil

# ====== USER SETTINGS ======
exe_file = "bobprime.exe"  # change this if your .exe has a different name
pyinstxtractor_path = "pyinstxtractor.py"  # must be in same folder
output_folder = "decompiled_output"
# ===========================

def run_pyinstxtractor(exe_file):
    print(f"[*] Extracting {exe_file}...")
    subprocess.run(["python", pyinstxtractor_path, exe_file])

def find_pyc_files(extracted_dir):
    pyc_files = []
    for root, dirs, files in os.walk(extracted_dir):
        for file in files:
            if file.endswith(".pyc"):
                pyc_files.append(os.path.join(root, file))
    return pyc_files

def decompile_pyc_files(pyc_files, output_dir):
    os.makedirs(output_dir, exist_ok=True)
    for pyc in pyc_files:
        output_file = os.path.join(output_dir, os.path.basename(pyc).replace(".pyc", ".py"))
        print(f"[+] Decompiling {pyc} → {output_file}")
        with open(output_file, "w", encoding="utf-8") as f:
            subprocess.run(["uncompyle6", pyc], stdout=f)

def main():
    extracted_dir = exe_file + "_extracted"
    
    run_pyinstxtractor(exe_file)
    
    if not os.path.isdir(extracted_dir):
        print("[-] Extraction failed or folder not found.")
        return
    
    pyc_files = find_pyc_files(extracted_dir)
    if not pyc_files:
        print("[-] No .pyc files found.")
        return
    
    decompile_pyc_files(pyc_files, output_folder)
    print(f"[✔] All files decompiled to: {output_folder}")

if __name__ == "__main__":
    main()

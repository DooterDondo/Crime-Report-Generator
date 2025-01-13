Crime Data Analysis Project:
This project analyzes crime data, groups the data by NCIC code ranges, districts, and beats, and visualizes the results. The project includes functionalities to read crime data from CSV files, generate crime reports, create JSON files of the results, and display bar charts for specified NCIC codes.

Getting Started
Prerequisites
Make sure you have the following libraries installed:

csv

json

matplotlib

datetime

You can install matplotlib using pip:
pip install matplotlib
Importing Libraries
python
import csv
import json
import matplotlib.pyplot as plt
from datetime import datetime
Functions
Read CSV File
python
def read_csv_file(filename):
    # Read and return data from the CSV file
    data = []
    with open(filename, 'r') as file:
        csv_reader = csv.DictReader(file)
        for row in csv_reader:
            data.append(row)
    return data
Display Crime Report
python
def display_crime_report(data):
    # Displaying crime report with specified groupings
    ncic_groups = {}
    district_counts = {}
    beat_counts = {}

    for row in data:
        # Grouping by NCIC code
        ncic_code = int(row['ucr_ncic_code'])
        group = (ncic_code // 1000) * 1000
        ncic_groups[group] = ncic_groups.get(group, 0) + 1

        # Count by district
        district = row['district']
        district_counts[district] = district_counts.get(district, 0) + 1

        # Count by beat
        beat = row['beat'].strip()
        beat_counts[beat] = beat_counts.get(beat, 0) + 1

    # Print counts of crimes grouped by NCIC code ranges, districts, and beats
    print("Crime Report:")
    print("\nCrimes grouped by NCIC code:")
    for group, count in sorted(ncic_groups.items()):
        print(f"{group}-{group+999}: {count}")

    print("\nCrimes by district:")
    for district, count in sorted(district_counts.items()):
        print(f"District {district}: {count}")

    print("\nCrimes by beat:")
    for beat, count in sorted(beat_counts.items()):
        print(f"Beat {beat}: {count}")

    # Return a dictionary containing the grouped data for JSON serialization
    return {
        "ncic_groups": ncic_groups,
        "district_counts": district_counts,
        "beat_counts": beat_counts
    }
Save to JSON
python
def save_to_json(data, filename):
    """Save data to a JSON file."""
    with open(filename, 'w') as file:
        json.dump(data, file, indent=4)
    print(f"Data saved to {filename}")
Display Crimes by Beat
python
def display_crimes_by_beat(data, beat):
    """Display all crimes for a specific beat."""
    print(f"Crimes in Beat {beat}:")
    for row in data:
        if row['beat'].strip() == beat:
            print(f"Date: {row['cdatetime']}, Crime: {row['crimedescr']}")
Create Bar Chart
python
def create_bar_chart(data, ncic_codes, title):
    """Create and save a bar chart for specified NCIC codes."""
    counts = {code: sum(1 for row in data if row['ucr_ncic_code'] == code) for code in ncic_codes}
    
    plt.figure(figsize=(10, 6))
    plt.bar(counts.keys(), counts.values())
    plt.title(title)
    plt.xlabel('NCIC Codes')
    plt.ylabel('Count')
    plt.savefig(f"{title}.png")
    plt.show()
Main Function
python
def main():
    filename = input("Enter the name of the CSV file: ")
    data = read_csv_file(filename)

    while True:
        print("\nMenu:")
        print("1. Display Crime Report")
        print("2. Display Crimes by Beat")
        print("3. Create Bar Chart")
        print("4. Quit")

        choice = input("Enter your choice (1-4): ")

        if choice == '1':
            report_data = display_crime_report(data)
            month = datetime.now().strftime("%B")
            save_to_json(report_data, f"{month}.json")
        elif choice == '2':
            beat = input("Enter beat number, ensure the letter attached is in uppercase: ")
            display_crimes_by_beat(data, beat)
        elif choice == '3':
            ncic_codes = [input(f"Enter NCIC code {i+1}: ") for i in range(5)]
            title = input("Enter chart title: ")
            create_bar_chart(data, ncic_codes, title)
        elif choice == '4':
            print("Exiting program.")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    main()

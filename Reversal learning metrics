# This script is used to calculate metrics in the reversal learning cognitive task （Jingying/12-28-2022)

import os
import csv
import pandas as pd

def calculate_metrics(df):
    """Calculate all RL metrics for a dataframe"""
    acq = df[df.TrialType == 'acquisition']
    rev = df[df.TrialType == 'reversal']
    
    # Basic metrics
    metrics = [
        df['Visit#'].iloc[1], df.Date.iloc[1],
        sum((acq.Correct == 'incorrect')), sum((rev.Correct == 'incorrect')),
        len(acq), len(rev),
        sum((acq.Correct == 'miss')), sum((rev.Correct == 'miss'))
    ]
    
    # Perseverative errors
    first_correct = df[(df.TrialType == 'reversal') & (df.Correct == 'correct')].index
    if len(first_correct) > 0:
        before = df[:first_correct[0]]
        pe = sum((before.TrialType == 'reversal') & (before.Correct == 'incorrect'))
        re = sum((df[first_correct[0]:].TrialType == 'reversal') & 
                (df[first_correct[0]:].Correct == 'incorrect'))
    else:
        pe = re = 0
    metrics.extend([pe, re])
    
    # Shift errors
    def count_shift_errors(phase, error_type):
        indices = df[(df.TrialType == phase) & (df.Feedback == 'inacc')].index
        count = 0
        for i in indices:
            if i < len(df) - 1 and df.Correct.iloc[i + 1] == error_type:
                count += 1
        return count
    
    shift_errors = [
        count_shift_errors('acquisition', 'incorrect'),
        count_shift_errors('reversal', 'incorrect'), 
        count_shift_errors('acquisition', 'miss'),
        count_shift_errors('reversal', 'miss')
    ]
    metrics.extend(shift_errors)
    
    return metrics

# Main processing
path = r'the location save your reversal learning files'
output_dir = r'your\output\directory\path'  # Replace with your desired output directory
output_file = os.path.join(output_dir, 'computed_data.csv')
header = ['ID', 'RL_Visit', 'RL_TestDate', 'RL_IR_acq', 'RL_IR_rev', 
          'RL_TC_acq', 'RL_TC_rev', 'RL_MR_acq', 'RL_MR_rev', 
          'RL_PE_rev', 'RL_RE_rev', 'RL_LSE_inacc_acq', 'RL_LSE_inacc_rev',
          'RL_LSE_miss_acq', 'RL_LSE_miss_rev']

all_results = []
for folder in os.listdir(path):
    folder_path = os.path.join(path, folder)
    if not os.path.isdir(folder_path):
        continue
        
    for filename in os.listdir(folder_path):
        if 'PRL_' in filename:
            file_path = os.path.join(folder_path, filename)
            df = pd.read_table(file_path, sep='\t')
            result = [filename] + calculate_metrics(df)
            all_results.append(result)

# Write results
with open(output_file, 'w', newline='') as f:
    writer = csv.writer(f)
    writer.writerow(header)
    writer.writerows(all_results)

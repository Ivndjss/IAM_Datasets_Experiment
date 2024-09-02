# IAM_Datasets_Experiment

## First is install the import/packages that need
python language: https://www.python.org/downloads/release/python-3125/
installation of openCV and pytesseract: 

    pip install opencv-python pytesseract

Change the directory andd path of the code.
here is the code:

        import cv2
        import pytesseract
        import os
        import csv
        
        # Specify the path to the Tesseract executable
        pytesseract.pytesseract.tesseract_cmd = r'C:\Program Files\Tesseract-OCR\tesseract.exe'
        
        # Define the folder paths
        input_folder_path = r'C:\thesis_datasets\Datasets_first _batch'
        output_folder_path = r'C:\thesis_datasets\annotated_files'
        CSV_folder_path = r'C:\thesis_datasets\annotated_files\CSV'
        JPG_folder_path = r'C:\thesis_datasets\annotated_files\JPG'
        TXT_folder_path = r'C:\thesis_datasets\annotated_files\TXT'
        
        # Check if the output folder exists, if not, create it
        if not os.path.exists(output_folder_path):
            os.makedirs(output_folder_path)
        
        # Iterate through each file in the input folder
        for filename in os.listdir(input_folder_path):
            if filename.endswith('.jpg'):
                file_path = os.path.join(input_folder_path, filename)
                print(f"Processing file: {file_path}")
        
                # Load the image
                image = cv2.imread(file_path)
                if image is None:
                    print(f"Error: Could not load image {file_path}.")
                    continue
        
                # Convert the image to grayscale
                gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
        
                # Apply thresholding to get a binary image
                _, binary_image = cv2.threshold(gray, 150, 255, cv2.THRESH_BINARY_INV)
        
                # Detect text boxes
                boxes = pytesseract.image_to_boxes(binary_image)
        
                # Draw rectangles around detected text
                for box in boxes.splitlines():
                    box = box.split(' ')
                    x, y, w, h = int(box[1]), int(box[2]), int(box[3]), int(box[4])
                    cv2.rectangle(image, (x, image.shape[0] - y), (w, image.shape[0] - h), (0, 255, 0), 2)
        
                # Extract text from the image
                text = pytesseract.image_to_string(binary_image).strip()
        
                # Use the first word from the extracted text for naming
                annotated_word = text.split()[0] if text else 'no_text'
        
                # Define the output file names
                base_filename = os.path.splitext(filename)[0]
                #annotated_image_path = os.path.join(output_folder_path, f'{base_filename}_{annotated_word}.jpg')
                text_file_path = os.path.join(TXT_folder_path, f'{base_filename}.txt')
                csv_file_path = os.path.join(CSV_folder_path, f'{base_filename}.csv')
                jpg_file_path = os.path.join(JPG_folder_path, f'{base_filename}.jpg')
        
                # Save the annotated image
                cv2.imwrite(jpg_file_path, image)
                print(f"Annotated image saved as {jpg_file_path}")
        
                # Save the text to a TXT file
                with open(text_file_path, 'w') as file:
                    file.write(text)
                print(f"Text extracted and saved as {text_file_path}")
        
                # Save the text to a CSV file
                with open(csv_file_path, 'w', newline='') as csvfile:
                    csvwriter = csv.writer(csvfile)
                    csvwriter.writerow(['Extracted Text'])
                    csvwriter.writerow([text])
                print(f"Text extracted and saved as {csv_file_path}")

the data uploaded is not a sensitive data so you can use it as sample.

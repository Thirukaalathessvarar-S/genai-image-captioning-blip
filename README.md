## Prototype Development for Image Captioning Using the BLIP Model and Gradio Framework

### AIM:
To design and deploy a prototype application for image captioning by utilizing the BLIP image-captioning model and integrating it with the Gradio UI framework for user interaction and evaluation.

### PROBLEM STATEMENT:
Image captioning is the task of generating a textual description for a given image. This is crucial for applications like accessibility tools for visually impaired users, content generation, and image indexing. Traditional systems often rely on predefined labels or are limited in context understanding. By leveraging the BLIP model—a state-of-the-art vision-language pretraining model—this project aims to create an intuitive and efficient application for real-time image captioning, accessible via the Gradio interface.

### DESIGN STEPS:
#### STEP 1: 
Import the necessary libraries such as Gradio, Requests, and other utility modules for handling images and API communication.

#### STEP 2: 
Define a function get_completion() to send image data to the BLIP API endpoint and receive the generated caption response.

#### STEP 3: 
Create a helper function image_to_base64_str() to convert uploaded images into base64 strings compatible with the API request format.

#### STEP 4: 
Develop a main function captioner(image) that processes the image through the BLIP model and retrieves the generated caption.

#### STEP 5: 
Build a Gradio interface with gr.Interface(), allowing users to upload an image, display captions, and visualize example outputs.

### PROGRAM:
```
import os
import io
import IPython.display
from PIL import Image
import base64 
from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv()) # read local .env file
hf_api_key = os.environ['HF_API_KEY']
```

```
# Helper functions
import requests, json

#Image-to-text endpoint
def get_completion(inputs, parameters=None, ENDPOINT_URL=os.environ['HF_API_ITT_BASE']):
    headers = {
      "Authorization": f"Bearer {hf_api_key}",
      "Content-Type": "application/json"
    }
    data = { "inputs": inputs }
    if parameters is not None:
        data.update({"parameters": parameters})
    response = requests.request("POST",
                                ENDPOINT_URL,
                                headers=headers,
                                data=json.dumps(data))
    return json.loads(response.content.decode("utf-8"))
```

```
image_url = "https://free-images.com/sm/9596/dog_animal_greyhound_983023.jpg"
display(IPython.display.Image(url=image_url))
get_completion(image_url)
```

```
import gradio as gr
import time
import os
import io
import base64

def image_to_base64_str(pil_image):
    byte_arr = io.BytesIO()
    pil_image.save(byte_arr, format='PNG')
    byte_arr = byte_arr.getvalue()
    return str(base64.b64encode(byte_arr).decode('utf-8'))

def captioner(image):
    # Enhanced captioner function with timing and metadata
    start_time = time.time()
    
    base64_image = image_to_base64_str(image)
    result = get_completion(base64_image)
    
    # Calculate processing time
    processing_time = time.time() - start_time
    
    # Extract caption
    caption = result[0]['generated_text']
    
    # Create enhanced output with metadata
    enhanced_output = f"📝 Caption: {caption}\n\n"
    enhanced_output += f"⏱️ Processing Time: {processing_time:.2f} seconds\n"
    enhanced_output += f"🤖 Model: Salesforce/BLIP\n"
    enhanced_output += f"📊 Caption Length: {len(caption)} characters"
    
    return enhanced_output

gr.close_all()
demo = gr.Interface(
    fn=captioner,
    inputs=[gr.Image(label="Upload Image", type="pil")],
    outputs=[gr.Textbox(label="Generated Caption with Metadata", lines=8)],
    title="🖼️ Enhanced Image Captioning Prototype with BLIP",
    description="Upload any image to generate descriptive captions using the BLIP model. This enhanced prototype includes processing time and caption metadata.",
    allow_flagging="never",
    examples=["christmas_dog.jpeg", "bird_flight.jpeg", "cow.jpeg"]
)

demo.launch(share=True, server_port=int(os.environ['PORT1']))
```

```
gr.close_all()
```
### OUTPUT:
<img width="1285" height="652" alt="image" src="https://github.com/user-attachments/assets/cd40e975-8019-4093-a60a-52da1fad3ccc" />

<img width="1010" height="647" alt="image" src="https://github.com/user-attachments/assets/3f0234e3-e354-41f1-917b-19d3b16446fb" />

<img width="1003" height="540" alt="image" src="https://github.com/user-attachments/assets/04f15cee-b627-440f-bd38-0c996de82dc7" />


### RESULT:
Thus, the image captioning application using the BLIP model was successfully developed and deployed with Gradio. The system efficiently generates descriptive captions for images, demonstrating the practical use of multimodal AI in automation and accessibility.

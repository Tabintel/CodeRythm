---
title: "How to Filter Image Uploads using PixLab NSFW API & Next.js"
datePublished: Fri Jun 28 2024 08:46:32 GMT+0000 (Coordinated Universal Time)
cuid: clxyg8opg00090ajzh1e806is
slug: how-to-filter-image-uploads-using-pixlab-nsfw-api-nextjs
canonical: https://dev.to/envitab/how-to-filter-image-uploads-using-pixlab-nsfw-api-nextjs-2ldf
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1719564228606/99d5ff60-aa47-4029-aae4-30d7d927a632.png
tags: web-development, apis, nextjs, pixlab

---

In this tutorial, we are building a Next.js application - PicFil, that filters image uploads with [PixLab's NSFW API](https://pixlab.io/). Users can upload an image or enter a link to the image they want to check.

When the check is complete, if the image is NSFW (Not Safe For Work), it will be displayed with a blur and a score ranging from 0 to 1. The closer the score is to 1, the higher the image is NSFW, and vice versa.

At the end of this tutorial, you will understand how to integrate PixLab APIs into your projects to enhance security and other use cases.

Let's get started!

[PixLab NSFW Endpoint](https://pixlab.io/cmd?id=nsfw)

## Prerequisites

* Understanding of Javascript and Next.js
    
* NPM installed on your machine
    
* A PixLab account. Sign up here [https://pixlab.io/](https://pixlab.io/)
    
* Cloudinary cloud account - for uploading the images
    
* A Code editor - VS Code, NeoVim, or your favorite!
    
* The desire to learn and build! 😎
    

## What is PixLab?

[PixLab](https://pixlab.io/) is a Machine Learning SaaS platform that offers Computer Vision and Media Processing APIs either via a straightforward HTTP RESTful API or offline SDK via the SOD Embedded CV library.

### How the PixLab NSFW API Works

The PixLab NSFW API is designed to detect inappropriate content in images or video frames, such as nudity and adult material. This API is useful for developers who need to automate the process of filtering user uploads to ensure they meet community guidelines or content policies.

**Key Features:**

* **Detection:** The API analyzes the content of an image or video frame and returns an NSFW score between 0 and 1. A score closer to 1 indicates a higher likelihood of inappropriate content.
    
* **Integration with Other Endpoints:** The NSFW API can be combined with other PixLab media processing endpoints, such as [blur](https://pixlab.io/cmd?id=blur), [encrypt](https://pixlab.io/cmd?id=encrypt), or [mogrify](https://pixlab.io/cmd?id=mogrify), to censor images based on their NSFW score.
    
* **HTTP Methods:** The API supports GET and POST methods, allowing flexibility in sending the image data (either as a URL or a direct file upload).
    

**Request Parameters:**

* **Required:**
    
    * **img:** The image URL to be analyzed (for GET requests).
        
    * **file:** The image file to be uploaded (for POST requests).
        
    * **key:** Your PixLab API key.
        

**Response:** The API returns a JSON object containing:

* **status:** The request status (e.g., 200 for success).
    
* **score:** The NSFW score of the image.
    
* **error:** Any error messages if the request fails.
    

Now, let's integrate the PixLab NSFW API into the Next.js application to filter image uploads based on their NSFW score.

### Step 1: Create a Next.js app

Open your terminal and run these commands to create a new directory and a Next.js app.

```plaintext
mkdir nsfw-filter
cd nsfw-filter
npx create-next-app@latest .
```

![next.app](https://hackmd.io/_uploads/B1MEoIxrA.png align="left")

Install Axios, which we will use to make HTTP requests to the PixLab API. Run this command:

```plaintext
npm install axios
```

Axios is used to create a toast and inform the user of the result when the image is uploaded and checked with the PixLab NSFW API. Create a `provider` folder in the **app** directory, then enter this code in a `ToasterProvider.jsx` file.

```jsx
"use client";
import { Toaster } from "react-hot-toast";
const ToasterProvider = () => {
  return <Toaster/>
};


export default ToasterProvider;
```

### Step 2: Developing the API

Next, we develop an API client to make a `POST` request to the PixLab NSFW [api.pixlab.io](http://api.pixlab.io) endpoint, communicate with the API, and avoid CORS errors.

It takes an `image` and a `key` as the required terms and returns the data. You can find this in the [route.jsx](https://github.com/Tabintel/the-picfill-app/blob/main/app/(Home)/_components/index.jsx) file in the `app` directory.

![route](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1opmnyqg24n2el41k80s.png align="left")

In the **app** directory as shown above, create an `api` folder and a `nsfw` folder inside it; then create a `route.jsx` file and enter the code:

```jsx
// app/api/nsfw/route.js
import { NextResponse } from "next/server";
import axios from "axios";

export async function POST(req) {
  const { img, key } = await req.json();

  try {
    const response = await axios.post(
      `https://api.pixlab.io/NSFW?key=${key}`,
      { img, key },
      {
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    return NextResponse.json(response.data);
  } catch (error) {
    return NextResponse.json(
      {
        message: error.response?.data?.message || error.message,
      },
      { status: error.response?.status || 500 }
    );
  }
}
```

This project setup is on the [GitHub repository](https://github.com/Tabintel/the-picfill-app)

### Step 3: Developing the App Router

The app router houses the client side and also the server-side folder. On the server side, we created a route at `/api/nsfw/route.jsx`. This file sends a POST request to the PixLab NSFW API to prevent [CORS errors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS/Errors).

#### Checking the image with PixLab API.

```http
  POST /api/nsfw
```

| Parameter | Type | Description |
| --- | --- | --- |
| `img` | `string` | **Required**.User's uploaded image |
| `key` | `string` | **Required**. Key form PIXLAB's api |

**Response:**

* Status: 200 OK
    
* Body:
    
    ```json
    {
      "data": {
        "status": "pixLab_status",
        "score": "pixlab_score"
      }
    }
    ```
    

#### Example Request

```http
POST /api/nsfw
Content-Type: application/json

{
  "img": "user's_image",
  "key": "pxi_lab_key",
}
```

### Step 4: Developing the Client-side of the app

Here, we create a callback function from React and set the result from the image upload.

#### Initializing States

We create states to manage the lifecycle of the components.

* The Image state
    
* The Loading state
    

**States:**

```jsx
const [image, setImage] = useState("");
const [result, setResult] = useState(null);
const [loading, setLoading] = useState(false);
```

#### Uploading images using `next-cloudinary`

```jsx
**next-cloudinary:**
<CldUploadWidget
multiple
onSuccess={handleUpload}
uploadPreset="dl93zl9fn"
folder="uploads"
sources={["local", "url", "camera"]} >
{({ open }) => {
return (
<div
className="w-full cursor-pointer border-dotted px-4 md:px-8 border-4 border-[rgba(0,0,0,.2)] h-[250px] flex flex-col gap-4 items-center justify-center
"
onClick={() => open()} >
<CiImageOn fontSize={"38px"} />
<span className="text-sm text-center">
Upload a fileNo file chosen or drag and drop PNG, JPG,
GIF up to 10MB
</span>
</div>
);
}}
</CldUploadWidget>
```

### Step 5: Interacting with the Nextjs server

Here is the code we use in developing the application's logic, which will interact with the server.

```jsx

    // Retrieve PixLab API Key from the environment variable

  const tempKey = process.env.NEXT_PUBLIC_PIXLAB_API_KEY;
  const handleTextImage = async () => {
    setLoading(true);
    try {
      const { data } = await axios.post("/api/nswft", {
        img: image,
        key: tempKey,
      });
      setResult(data);
      toast.success("Image test successfull!!");
      setLoading(false);
    } catch (error) {
      setLoading(false);
      toast.error(
        error.response && error.response.data.message
          ? error.response.data.message
          : error.message
      );
    }
  };
```

### Setting up the Logic

We make use of [ternary operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator) to check for conditions in the rendering of the images.

So, in the first line, we are saying that if the state (image) is empty and the result from the server is not defined, we should render the div. This div is also conditioned to check if the score response from the server is 1. If it is 1, then we should display the image without blurring it else, we should blur the image. Otherwise, we should return the image when it has not yet been sent to the server.

```jsx
    {image !== "" && result ? (
            <div className="w-full">
              {result?.score === 1 ? (
                <Image
                  alt="Cotion"
                  width={0}
                  sizes="100vw"
                  height={0}
                  loading="lazy"
                  src={image}
                  // blurDataURL={image | ""}
                  className="h-[250px] w-full md:mx-auto object-cover"
                />
              ) : (
                <Image
                  alt="Cotion"
                  width={0}
                  sizes="100vw"
                  height={0}
                  loading="lazy"
                  style={{ filter: "blur(10px" }}
                  src={image}
                  className="h-[250px] w-full md:mx-auto object-cover"
                />
              )}
            </div>
          ) : (
            <>
              {image ? (
                <Image
                  alt="Cotion"
                  width={0}
                  sizes="100vw"
                  height={0}
                  loading="lazy"
                  src={image}
                  // blurDataURL={image | ""}
                  className="h-[250px] w-full md:mx-auto object-cover"
                />
              ) : (
                <CldUploadWidget
                  multiple
                  onSuccess={handleUpload}
                  uploadPreset="dl93zl9fn"
                  folder="uploads"
                  sources={["local", "url", "camera"]}
                >
                  {({ open }) => {
                    return (
                      <div
                        className="w-full cursor-pointer border-dotted px-4  md:px-8 border-4 border-[rgba(0,0,0,.2)] h-[250px] flex flex-col gap-4 items-center justify-center
          "
                        onClick={() => open()}
                      >
                        <CiImageOn fontSize={"38px"} />
                        <span className="text-sm text-center">
                          Upload a fileNo file chosen or drag and drop PNG, JPG,
                          GIF up to 10MB
                        </span>
                      </div>
                    );
                  }}
                </CldUploadWidget>
              )}
            </>
          )}
```

---

Then, enter this code in the `index.js` file inside the `app/(Home)/_components` directory, which handles the application's UI.

```jsx
return (
    <div className="flex flex-col relative w-full gap-4">
      {loading && <Loader />}
      <div className="w-full h-[100vh] flex items-center justify-center gap-8">
        <div className="w-[90%] md:w-[500px] max-w-custom_1 mx-auto flex items-start justify-center flex-col gap-8">
          <h2 className="text-4xl md:text-5xl font-booking_font4 text-dark">
            PicFil
            <span className="text-sm block font-booking_font text-start">
              NSFW image filter, powered by PixLab API
            </span>
          </h2>
          {/* <Image */}

          {image !== "" && result ? (
            <div className="w-full">
              {result?.score === 1 ? (
                <Image
                  alt="Cotion"
                  width={0}
                  sizes="100vw"
                  height={0}
                  loading="lazy"
                  src={image}
                  // blurDataURL={image | ""}
                  className="h-[250px] w-full md:mx-auto object-cover"
                />
              ) : (
                <Image
                  alt="Cotion"
                  width={0}
                  sizes="100vw"
                  height={0}
                  loading="lazy"
                  style={{ filter: "blur(10px" }}
                  src={image}
                  className="h-[250px] w-full md:mx-auto object-cover"
                />
              )}
            </div>
          ) : (
            <>
              {image ? (
                <Image
                  alt="Cotion"
                  width={0}
                  sizes="100vw"
                  height={0}
                  loading="lazy"
                  src={image}
                  // blurDataURL={image | ""}
                  className="h-[250px] w-full md:mx-auto object-cover"
                />
              ) : (
                <CldUploadWidget
                  multiple
                  onSuccess={handleUpload}
                  uploadPreset="dl93zl9fn"
                  folder="uploads"
                  sources={["local", "url", "camera"]}
                >
                  {({ open }) => {
                    return (
                      <div
                        className="w-full cursor-pointer border-dotted px-4  md:px-8 border-4 border-[rgba(0,0,0,.2)] h-[250px] flex flex-col gap-4 items-center justify-center
          "
                        onClick={() => open()}
                      >
                        <CiImageOn fontSize={"38px"} />
                        <span className="text-sm text-center">
                          Upload a fileNo file chosen or drag and drop PNG, JPG,
                          GIF up to 10MB
                        </span>
                      </div>
                    );
                  }}
                </CldUploadWidget>
              )}
            </>
          )}
          {result && image !== "" && (
            <div className="w-full">
              <h2 className="text-2xl font-booking_font4 text-dark">
                <span className="font-booking_font text-base">Your Score:</span>{" "}
                <span>{result?.score}</span>
              </h2>
            </div>
          )}
          <div className="flex w-full flex-col sm:flex-row items-center gap-4 justify-between">
            <input
              className="h-[80px] input border w-full"
              onChange={(e) => {
                setResult(null);
                setImage(e.target.value);
              }}
              type="text"
              name={"image"}
              value={image}
              placeholder="Enter you Image Url Here"
            />
            <button
              disabled={image === ""}
              onClick={handleTextImage}
              className="w-full font-bold md:w-[250px] h-[55px] rounded-[10px] text-white btn btn-1"
            >
              {loading ? (
                <span className="flex justify-center items-center gap-2">
                  <ThreeDots
                    height="25"
                    width="25"
                    radius="10"
                    color={"#fff"}
                    ariaLabel="three-dots-loading"
                    wrapperStyle={{}}
                    wrapperClassName=""
                    visible={true}
                  />
                  Uploading
                </span>
              ) : (
                "Check Image"
              )}
            </button>
          </div>
        </div>
      </div>
      <div className="w-full sticky bottom-0 py-4 bg-[#000] text-sm text-white flex items-center justify-center">
        © 2024 PicFil. All rights reserved.
      </div>
    </div>
  );
};

export default MainContent;
```

### Step 6: Setting up your PixLab account

Go to the [PixLab website](https://console.pixlab.io/dashboard) and enter your username and email to create an account.

In your dashboard, click on **API Keys** like so:

![PixLab](https://hackmd.io/_uploads/HJDAaNJSR.png align="left")

Then click on **Generate API Key** to get the key we will use for the Next.js application.

![API Key](https://hackmd.io/_uploads/ryt-1rkSC.png align="left")

You will then need to select the subscription plan from the options below:

![plans](https://hackmd.io/_uploads/H1WF1SyrC.png align="left")

#### The API key and `env` file

Take note of it, as we'll use it to integrate PixLab NSFW API into the web application.

In the root directory of your Next.js project, create a new file named `.env.local` and enter this to define the environment variable:

```shell
NEXT_PUBLIC_PIXLAB_API_KEY=your_pixlab_api_key_here
NEXT_PUBLIC_CLOUDINARY_CLOUD_NAME=your_cloudinary_name_here
```

> Replace "your\_pixlab\_api\_key\_here" with your actual PixLab API key, and add `.env.local` to your .gitignore file

### How to Run the Application

Start the development server by running:

```shell
npm run dev
```

The web app can be accessed in your browser with this URL

[http://localhost:3000](http://localhost:3000)

When you open the URL in your browser, you can upload an image directly from your browser's UI or enter a link (URL) to an image to test the application.

![PicFil app](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9ratl9xvzsd7hjaj2d5h.png align="left")

In this demo, an image is uploaded, and after clicking on **Check Image**, the PixLab NSFW API processes the image, blurs it and displays the score like so:

![example 1](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w249sd34jyq1kjfmkqp9.png align="left")

Let's try out another image:

![example 2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/kkzxnv9yavndy2pr0sz3.png align="left")

Note the difference in the scores. As noted on the [PixLab NSFW documentation](https://pixlab.io/cmd?id=nsfw), the score ranges from 0 to 1. A score closer to **1** means the photo is more likely NFSW, and vice versa for a score closer to **0**.

And that's it! We have successfully integrated the PixLab NSFW API into the web application.

> *Images used are from Unsplash by* [*Gabin Vallet*](https://unsplash.com/@gabinvallet)*,* [*Brian Lawson*](https://unsplash.com/@visualartery) *and* [*Redd F*](https://unsplash.com/@raddfilms?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)*.*

To clone the project and run it locally, open your terminal and run this command:

```shell
git clone https://github.com/Tabintel/the-picfill-app
```

Then run `npm install` to install all the project dependencies and `npm run dev` to run the web app.

Get the full source code on [GitHub.](https://github.com/Tabintel/the-picfill-app)

With PixLab, you can enhance your applications with intelligent user experiences and implement robust content moderation systems. Whether using Javascript, Python, or other programming languages, PixLab provides the resources for your projects.

To get started:

* Create a [PixLab account](https://pixlab.io/start)
    
* Learn about the [PixLab API](https://pixlab.io/start), and the [API Reference Guide](https://pixlab.io/api).
    
* Check out the full list of [API endpoints](https://pixlab.io/cmdls) and other [use cases](https://pixlab.io/examples).
    

---
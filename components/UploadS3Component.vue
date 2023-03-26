<template>
    <div>
        <div v-if="uploading">
            <p>Subiendo archivo {{ percentage.toFixed(2) }}%</p>
        </div>
        <div v-if="uploading" class="meter">
            <span :style="`width: ${percentage.toFixed(2)}%`"></span>
        </div>
        <div v-if="finish">
            <p class="succ">Archivo Subido</p>
        </div>
        <label for="images" class="drop-container">
            <span class="drop-title">Drop files here</span>
            or
            <input type="file" id="images" class="file-input" @change="handleUpload($event.target.files)" required />
        </label>

    </div>
</template>
  
<script>
import { s3 } from "@/plugins/aws";

export default {
    data() {
        return {
            uploading: false,
            finish: false,
            percentage: 0,
        };
    },
    methods: {
        async handleUpload(files) {
            console.log(process.env.AWS_S3_BUCKET_NAME);

            this.uploading = true;
            const file = files[0];
            const filename = `${Date.now()}-${file.name}`;
            const uploadParams = {
                Bucket: process.env.AWS_S3_BUCKET_NAME,
                Key: filename,
                ContentType: file.type,
                Body: file,
            };
            const s3Upload = s3.upload(uploadParams);
            s3Upload.on("httpUploadProgress", (progress) => {
                this.percentage = (progress.loaded / progress.total) * 100;
            });
            try {
                const response = await s3Upload.promise();
                this.uploading = false;
                this.finish = true;
                this.$emit("upload-success", response.Location);
            } catch (error) {
                this.uploading = false;
                console.error(error);
            }
        },
    },
};
</script>

<style>
.meter {
    margin: auto;
    width: 600px;
    box-sizing: content-box;
    height: 20px;
    background: #555;
    border-radius: 25px;
    padding: 10px;
    box-shadow: inset 0 -1px 1px rgba(255, 255, 255, 0.3);
    margin-bottom: 30px;
}

.meter>span {
    display: block;
    height: 100%;
    border-top-right-radius: 8px;
    border-bottom-right-radius: 8px;
    border-top-left-radius: 20px;
    border-bottom-left-radius: 20px;
    background-color: rgb(43, 194, 83);
    background-image: linear-gradient(center bottom,
            rgb(43, 194, 83) 37%,
            rgb(84, 240, 84) 69%);
    box-shadow: inset 0 2px 9px rgba(255, 255, 255, 0.3),
        inset 0 -2px 6px rgba(0, 0, 0, 0.4);
    position: relative;
    overflow: hidden;
}

.meter>span:after,
.animate>span>span {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background-image: linear-gradient(-45deg,
            rgba(255, 255, 255, 0.2) 25%,
            transparent 25%,
            transparent 50%,
            rgba(255, 255, 255, 0.2) 50%,
            rgba(255, 255, 255, 0.2) 75%,
            transparent 75%,
            transparent);
    z-index: 1;
    background-size: 50px 50px;
    animation: move 2s linear infinite;
    border-top-right-radius: 8px;
    border-bottom-right-radius: 8px;
    border-top-left-radius: 20px;
    border-bottom-left-radius: 20px;
    overflow: hidden;
}

.animate>span:after {
    display: none;
}

@keyframes move {
    0% {
        background-position: 0 0;
    }

    100% {
        background-position: 50px 50px;
    }
}

.orange>span {
    background-image: linear-gradient(#f1a165, #f36d0a);
}

.red>span {
    background-image: linear-gradient(#f0a3a3, #f42323);
}

.nostripes>span>span,
.nostripes>span::after {
    background-image: none;
}

#page-wrap {
    width: 490px;
    margin: 80px auto;
}


/* Input */

  
  .drop-container {
    width: 700px;
    margin: auto;
    position: relative;
    display: flex;
    gap: 10px;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 200px;
    padding: 20px;
    border-radius: 10px;
    border: 2px dashed #555;
    color: #444;
    cursor: pointer;
    transition: background .2s ease-in-out, border .2s ease-in-out;
  }
  
  .drop-container:hover {
    background: #4d4d4d;
    border-color: #111;
  }
  
  .drop-container:hover .drop-title {
    color: #ffffff;
  }
  
  .drop-title {
    color: #999999;
    font-size: 20px;
    font-weight: bold;
    text-align: center;
    transition: color .2s ease-in-out;
  }
  
  input[type=file] {
    width: 350px;
    max-width: 100%;
    color: #444;
    padding: 5px;
    background: #fff;
    border-radius: 10px;
    border: 1px solid #555;
  }
  
  input[type=file]::file-selector-button {
    margin-right: 20px;
    border: none;
    background: #084cdf;
    padding: 10px 20px;
    border-radius: 10px;
    color: #fff;
    cursor: pointer;
    transition: background .2s ease-in-out;
  }
  
  input[type=file]::file-selector-button:hover {
    background: #0d45a5;
  }


/* message tag p*/ 
.succ {
    margin: 10px auto;
    width: 400px;
    font-size: 2rem;
    font-weight: 700;
    border-radius: 30px;
    background-color: rgb(62, 129, 237);
    color: white;
    padding: 20px;
}
</style>
**67.** How did you handle file uploads securely? What validations did you apply?

========== THIS SECTION IS THE ANSWER ==========

Validate file type by content (not just extension), enforce size limits, scan for malware, and store files outside the application server.

On the automotive marketplace, dealers uploaded vehicle images — up to 30 photos per listing. Our validation pipeline: first, the controller enforced a 10MB per-file limit and max 30 files per request via Spring's MultipartResolver config. Second, we validated the file's actual content type by reading the magic bytes (file header), not trusting the Content-Type header or file extension — someone could rename a .exe to .jpg. We only accepted JPEG, PNG, and WebP. Third, files were streamed directly to S3 (never stored on the application server's filesystem), with a pre-signed URL returned to the frontend. S3 bucket policy blocked public access; images were served through CloudFront with signed URLs. Fourth, we stripped EXIF metadata from uploaded images to prevent leaking GPS coordinates or camera info from dealer photos.

The trade-off: content-type validation by magic bytes adds processing time (~10ms per file), and EXIF stripping requires an image processing library. But these are essential — a single malicious file upload can compromise your server or leak user location data. The lesson: file upload is one of the most common attack vectors. Treat every uploaded file as hostile until proven safe.

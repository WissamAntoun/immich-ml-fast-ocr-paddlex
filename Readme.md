# Fast Inference for Immich ML OCR with PaddleX

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A solution to dramatically improve OCR inference performance in Immich ML by routing OCR requests to a PaddleX service, achieving ~80x speedup on GPU.

## Performance

- **~1s** inference time per image with 10 concurrent requests on RTX 3080ti 12GB GPU with PaddleX
- **80s+** with Onnxruntime GPU execution provider (default Immich ML backend)

## Full Guide

For detailed step-by-step instructions, please visit the comprehensive guide on my website:

**[Fast Inference for Immich ML OCR with PaddleX - Full Guide](https://wissamantoun.com/posts/guides/immich-fast-ocr-paddlex/)**

The guide covers:
- Context and problem explanation
- Complete setup instructions
- Docker configuration
- File modifications and mounting
- Testing and verification

## Quick Start

1. Clone this repository:
```bash
git clone https://github.com/WissamAntoun/immich-ml-fast-ocr-paddlex.git
cd immich-ml-fast-ocr-paddlex
```

2. Create the model cache directory:
```bash
mkdir -p model-cache
```

3. Build and start the PaddleX OCR service:
```bash
docker-compose build paddle-ocr
docker-compose up -d paddle-ocr
```

4. Update your Immich ML configuration and restart:
```bash
docker-compose up -d immich-machine-learning
```

## Repository Structure

```
.
├── paddle_ocr/
│   ├── Dockerfile
│   ├── paddlex_ocr_pipeline.py
│   ├── paddlex_ocr_result.py
│   └── OCR.yaml
├── immich_ml_main.py
├── docker-compose.yml
└── README.md
```

## Key Components

- **PaddleX OCR Service**: Optimized OCR pipeline using PaddleX with GPU acceleration
- **Modified Immich ML**: Routes OCR requests to PaddleX service via environment variable
- **Docker Compose**: Complete orchestration for both services

## Testing

Verify the PaddleX OCR service is working:

```bash
curl -sL "https://raw.githubusercontent.com/PaddlePaddle/PaddleOCR/main/docs/images/Banner.png" -o ./ocr_test.png

python -c "import requests, base64; r = requests.post('http://localhost:8866/ocr/', json={'file': base64.b64encode(open('./ocr_test.png', 'rb').read()).decode('utf-8'), 'fileType': 1, 'visualize': False}); result = r.json(); texts = result['result']['ocrResults'][0]['prunedResult']['rec_texts'] if r.status_code == 200 and 'result' in result else []; print(f'✓ OCR server is working - Found {len(texts)} text items:\n' + '\n'.join([f\"  - {text}\" for text in texts]) if texts else f'✗ OCR server failed: {r.status_code}')"
```

## Requirements

- Docker and Docker Compose
- NVIDIA GPU with CUDA support
- Immich instance running version 2.3.1 or later

## Known Issues

- Tested with Immich 2.3.1, PaddleX 3.3.4, and PaddleOCR 3.3.2
- Future versions may require path adjustments in volume mounts

## Related Issues

- [Immich GitHub Issue #23462](https://github.com/immich-app/immich/issues/23462)
- [Reddit Discussion](https://www.reddit.com/r/immich/comments/1omj5ue/ocr_job_is_slow/)

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Author

**Wissam Antoun**

- Website: [wissamantoun.com](https://wissamantoun.com)
- GitHub: [@WissamAntoun](https://github.com/WissamAntoun)
- Twitter: [@wissam_antoun](https://twitter.com/wissam_antoun)

## 🙏 Acknowledgments

- [Immich](https://immich.app/) for the amazing self-hosted photo management solution
- [PaddleX](https://paddlepaddle.github.io/PaddleX/latest/) for the optimized OCR pipeline
- The open-source community for continuous support and feedback

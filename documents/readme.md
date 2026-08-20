# Local Corpus

This directory contains the PDF reference corpus used by the notebooks:

- `book1.pdf`
- `book2.pdf`
- `book3.pdf`

The notebooks load these files with `PyPDFLoader`, split the extracted text into overlapping chunks, and index the chunks in FAISS. To use a different corpus, replace these files or update the loader paths in the notebooks.

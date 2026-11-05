LatentGuard: DDoS Detection with a Benign-Only Adversarial Autoencoder

LatentGuard detects DDoS traffic using a small adversarial autoencoder trained only on benign packets. It fuses two signals, reconstruction error and a discriminator's deviation from normal, then tests the detector against a white-box adaptive attacker. A second notebook compares a graph-theory (kNN graph + GCN) encoder and measures cross-dataset generalization on NSL-KDD.

Author: Dharini Sharma, School of Computer Science and Engineering, Vellore Institute of Technology, India.

Repository contents
File	Description
LatentGuard_DDoS_Detection.ipynb	Part 1: data loading, feature engineering, adversarial autoencoder, fused scorer, adaptive attacker
LatentGuard_Part2_GraphTheory_Generalization.ipynb	Part 2: graph (GCN) encoder, cross-dataset test on NSL-KDD, per-dataset normalization fix
data.zip	ddos_detection_dataset.csv (our own capture) and its metadata

Both notebooks contain their executed outputs, so results are visible directly on GitHub.

Dataset

ddos_detection_dataset.csv holds 39,713 packets captured with Wireshark: 39,031 benign (label -1) and 682 attack (label 1). Benign traffic came from normal Windows use and attack traffic from hping3 flooding on Kali Linux, so labels come from the generating tool.

Column	Meaning
timestamp	Packet capture time
src_ip, dst_ip	Source and destination address (grouping key only, not a model feature)
protocol	TCP or UDP
packet_length	Packet size in bytes
tcp_flags	TCP flag field (empty for UDP)
label	-1 benign, 1 attack

Part 2 also uses the DoS subset of NSL-KDD, downloaded automatically by the notebook.

How to run (Google Colab)
Unzip data.zip to get ddos_detection_dataset.csv.
Open a notebook in Colab: File → Open notebook → GitHub tab, paste this repository's URL, and choose the notebook.
Run all cells. When prompted, upload ddos_detection_dataset.csv.
Run Part 1 first, then Part 2.

To run locally, place the CSV next to the notebook and install: pip install torch scikit-learn pandas numpy matplotlib.

Results summary
Experiment	Result
Part 1 baseline, our data	Accuracy 0.912, recall 0.619, ROC-AUC 0.741, false-positive rate 3.7%
Adaptive attacker vs discriminator alone	100% evasion
Adaptive attacker vs fused score	3.1% evasion
Part 2 baseline, within our data (9 shared features)	ROC-AUC 0.991, false-positive rate 3.4%
Part 2 baseline, NSL-KDD cold	ROC-AUC 0.960, false-positive rate 38.6%
Part 2 graph encoder, within our data	ROC-AUC 0.998, false-positive rate 5.7%
Part 2 graph encoder, NSL-KDD cold	ROC-AUC 0.902, false-positive rate 98.8%

Part 1 uses the full engineered feature set and Part 2 a shared 9-feature set, so their AUC values are not directly comparable.

Key findings
Fusing reconstruction and discriminator signals closes the discriminator's 100% evasion hole.
The graph encoder is more accurate within our data but about 8x slower, needs a batch of packets, and fails across datasets.
A unit-mismatch bug in the first cross-dataset run was found and fixed by normalizing each dataset against its own benign traffic.
Requirements

Python 3.9+, PyTorch, scikit-learn, pandas, numpy, matplotlib.

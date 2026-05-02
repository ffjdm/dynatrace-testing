Vagrant.configure("2") do |config|
  config.vm.box = "perk/ubuntu-2204-arm64"
  config.vm.hostname = "dynatrace-lab"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider "qemu" do |qe|
    qe.arch = "aarch64"
    qe.machine = "virt,accel=hvf"
    qe.cpu = "host"
    qe.memory = "4096"
    qe.smp = "2"
  end

  config.vm.provision "shell", inline: <<-SHELL
  set -eux

  apt-get update
  apt-get install -y curl wget unzip ca-certificates htop vim net-tools nginx stress-ng

  systemctl enable --now nginx

  cat >/usr/local/bin/generate-load.sh <<'EOF'
#!/usr/bin/env bash
set -euo pipefail

DURATION="${1:-60}"
URL="${2:-http://localhost/}"

echo "Generating HTTP load on ${URL} for ${DURATION}s..."

end=$((SECONDS + DURATION))

while [ "$SECONDS" -lt "$end" ]; do
  curl -s "$URL" >/dev/null || true
  sleep 1
done

echo "Done."
EOF

  chmod +x /usr/local/bin/generate-load.sh

  echo "Dynatrace lab base VM ready"
  uname -a
SHELL
end

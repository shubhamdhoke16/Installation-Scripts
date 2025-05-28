#!/bin/bash

: <<'readme'
This is a script for backing up files (5 days retention)
Usage: ./backup.sh <source_dir> <backup_dir>
readme

basic_installation() {
    echo "Installing zip..."
    if [ -f /etc/debian_version ]; then
        sudo apt-get update
        sudo apt-get install -y zip
    elif [ -f /etc/redhat-release ]; then
        sudo yum install -y zip
    else
        echo "Unsupported OS"
        exit 1
    fi
}

if ! command -v zip &> /dev/null; then
    echo "zip could not be found"
    basic_installation
fi

echo "zip is installed........................"

display_usage() {
    echo "Usage: $0 <source_dir> <backup_dir>"
}

if [ $# -ne 2 ]; then
    display_usage
    exit 1
fi

source_dir="$1"
backup_dir="$2"
timestamp=$(date +%Y-%m-%d_%H-%M-%S)

create_backup() {
    zip -r "${backup_dir}/backup_${timestamp}.zip" "${source_dir}" >/dev/null
    if [ $? -eq 0 ]; then
        echo "Backup generated successfully at ${backup_dir}/backup_${timestamp}.zip"
    else
        echo "Backup failed"
        exit 1
    fi
}

perform_cleanup() {
    backups=($(ls -t "${backup_dir}/backup_"*.zip 2>/dev/null))
    if [ "${#backups[@]}" -gt 5 ]; then
        echo "More than 5 backups found."
        backups_to_remove=("${backups[@]:5}")
        echo "Items to remove: ${#backups_to_remove[@]}"
        for backup in "${backups_to_remove[@]}"; do
            echo "Deleting ${backup}"
            rm -f "${backup}"
        done
    fi
}

create_backup
perform_cleanup

echo "All done."

echo "🔁 To run this script every minute via cron, use the following line:"
echo "* * * * * $(realpath "$0") $source_dir $backup_dir"

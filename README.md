# Borg exporter

Export borg information to prometheus.

## Dependencies

 * Prometheus (obviously)
 * Node Exporter with textfile collector
 * Borg (https://github.com/borgbackup/borg)

## Install


Copy `borg_exporter` to `/usr/local/bin`.

you need to change this part of script and generate list of your borgbackup repositories

```
REPOS=$(find /spool/backup/ -maxdepth 4 -type d | grep "VM-BACKUP/")

for REPOSITORY in $REPOS; do

	LIST=$(borg list "$REPOSITORY" |awk '{print $1}')
```

every element of list must be path to your borg backup repository 
```
borg info [element of list ] --last 1 

Copy the systemd unit to `/etc/systemd/system` and run 

```
systemctl enable prometheus-borg-exporter.timer
systemctl start prometheus-borg-exporter.timer
```

Alternative: Use `ExecStartPost` in your borg backupt timer itself to write our the metrics.

## Configure your node exporter

Make sure your node exporter uses `textfile` in `--collectors.enabled` and add the following parameter: `--collector.textfile.directory=/var/lib/node_exporter/textfile_collector`

## Example queries

```
borg_backup_chunks_total
borg_backup_last_date ## last_backup_date - 24h in epoch format
borg_backup_last_size
```


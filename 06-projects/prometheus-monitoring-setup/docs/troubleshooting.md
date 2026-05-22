🔧 Troubleshooting

Table

Issue	                  Command	                                              Solution
Service won't start	    sudo journalctl -u prometheus -f	                    Check logs for errors
Config syntax error	    promtool check config /etc/prometheus/prometheus.yml	Validate YAML
Target DOWN	            sudo systemctl status node_exporter	                  Restart service
Permission denied	      ls -la /var/lib/prometheus/	                          Check ownership

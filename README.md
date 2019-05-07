# Ansible Role: digitalocean kubeconfig

Gets the kubeconfig for a DigitalOcean Kubernetes cluster.

## Requirements

* You must have a Digital Ocean API key.
* The Digital Ocean API must be accessible.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`).

### API Token

```
digitalocean_api_token: '1234567890abscdefg'
```

Your Digital Ocean API token.

### Getting the kubeconfig

```yaml
digitalocean_kubeconfig:
  cluster: 'my_cluster_name'
  kubeconfig: '/path/to/save/kubeconfig.yaml'
```

where:

* **cluster**: The cluster name
* **kubeconfig**: The path to save the kubeconfig file.

## Dependencies

None.

## Example Playbook

A straightforward use would be:

    ---
    - hosts: cluster
      vars:
        digitalocean_api_token: "1234567890abscdefg"
        digitalocean_kubeconfig:
          cluster: "my_cluster_name"
          kubeconfig: "/path/to/save/kubeconfig.yaml"
      roles:
         - socketwench.digitalocean_kubeconfig    
      tasks:
         - name: Apply our definitions  
           k8s:
             kubeconfig: "/path/to/save/kubeconfig.yaml"
             src: "/path/to/my/k8s_definitions.yml"

Sometimes you want to write the kubeconfig to a temporary file or directory. In that case, you can leverage include_role or import_role in Ansible 2.x:

    ---
    - hosts: cluster
      vars:
        digitalocean_api_token: '1234567890abscdefg'
      tasks:
        - name: Create a temp directory to store files needed by the run
          tempfile:
            state: directory
          register: _run_temp_dir
        - name: Get the kubeconfig file.
          import_role:
            name: socketwench.digitalocean_kubeconfig
          vars:
            digitalocean_kubeconfig:
              cluster: "my_cluster_name"
              kubeconfig: "{{ _run_temp_dir.path }}/kubeconfig.yaml"
        - name: Apply our definitions  
          k8s:
            kubeconfig: "{{ _run_temp_dir.path }}/kubeconfig.yaml"
            src: "/path/to/my/k8s_definitions.yml"

## License

GPL v3

## Author Information

This role was created by [socketwench](https://deninet.com/) for [TEN7](https://ten7.com).

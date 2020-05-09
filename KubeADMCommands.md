[kubeadm reset](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-reset/)

     --cert-dir string     Default: "/etc/kubernetes/pki"
     The path to the directory where the certificates are stored. If specified, clean this directory.
     --cri-socket string     Default: "/var/run/dockershim.sock"
     The path to the CRI socket to use with crictl when cleaning up containers.
     --force
     Reset the node without prompting for confirmation.
     -h, --help
     help for reset
     --ignore-preflight-errors stringSlice
     A list of checks whose errors will be shown as warnings. Example: 'IsPrivilegedUser,Swap'. Value 'all' ignores errors from all checks.

[kubeadm token](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-token/)

     Bootstrap tokens are used for establishing bidirectional trust between a node joining the cluster and a master node, as described in authenticating with bootstrap tokens.
     
     kubeadm init creates an initial token with a 24-hour TTL. The following commands allow you to manage such a token and also to create and manage new ones.

     kubeadm token create
     kubeadm token delete
     kubeadm token generate
     kubeadm token list 

[kubadm join](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-join/)

     Command initialized a kubernetes worker node and joins it to a cluster.
     Example:
      kubeadm join <ipaddress>:6443 --token <token> --discovery-token-ca-cert-hash <SHA hash of the root cert>

      Note: 
      You can get the token has from the a master node by using openssl 
      Example:
      
      openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | \ openssl dgst -sha256 -hex | sed 's/^.* //'

     

     
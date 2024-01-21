# Playbook - Ansible

## Ansible Playbook

### Mail Server

1. Replace the <mark style="color:red;">`hosts:`</mark> option to <mark style="color:red;">**localhost**</mark> or other <mark style="color:red;">**host**</mark> of your choice.

```yaml
- name: Install Mailcow Mail Server
  hosts: web_server
  become: true
  any_errors_fatal: true
  max_fail_percentage: 0

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: System update
      apt:
        update_cache: yes
      changed_when: false
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Dist upgrade
      apt:
        upgrade: dist
      register: upgrade_output
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Install required packages
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - git
          - software-properties-common
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Add Docker APT key
      apt_key:
        url: https://download.docker.com/linux/{{ ansible_distribution|lower }}/gpg
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Add Docker APT repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/{{ ansible_distribution|lower }} {{ ansible_lsb.codename }} stable
        state: present
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Install Docker
      apt:
        name: docker-ce
        state: latest
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Download and Install Docker Compose
      get_url:
        url: https://github.com/docker/compose/releases/latest/download/docker-compose-Linux-x86_64
        dest: /usr/bin/docker-compose
        mode: '0755'
      when: ansible_distribution in ['Ubuntu', 'Debian']

    - name: Replace the /etc/hostname Configuration File
      become: yes
      copy:
        content: 'mail.projeto.com'
        dest: /etc/hostname
      diff: yes
      ignore_errors: yes

    - name: Replace the /etc/hosts Configuration File
      become: yes
      copy:
        content: '192.168.117.134 mail.projeto.com mail'
        dest: /etc/hosts
      diff: yes
      ignore_errors: yes

    - name: Clone Mailcow-Dockerized Repository
      git:
        repo: https://github.com/mailcow/mailcow-dockerized.git
        dest: /home/nuno/Documents/mailcow-dockerized
      become: yes

    - name: Navigate to Mailcow-Dockerized Directory
      shell: cd /home/nuno/Documents/mailcow-dockerized
      become: yes

    - name: Generate mailcow.conf File
      shell: ./generate_config.sh
      environment:
        MAILCOW_HOSTNAME: "mail.projeto.com"
        MAILCOW_TZ: "Europe/Lisbon"
        MAILCOW_BRANCH: "1"
      args:
        executable: /bin/bash
        chdir: /home/nuno/Documents/mailcow-dockerized
        creates: mailcow.conf
      tags:
        - skip_ansible_lint

    - name: Run docker-compose up -d
      command: docker-compose up -d
      args:
        chdir: /home/nuno/Documents/mailcow-dockerized
      become: yes
```

### DNS Master

1. Replace the <mark style="color:red;">`hosts:`</mark> option to <mark style="color:red;">**localhost**</mark> or the <mark style="color:red;">**host**</mark> of your <mark style="color:red;">**DNS Master**</mark> machine.

```yaml
- name: DNS Master Configuration
  hosts: dns_master

  tasks:
    - name: Add Mail Server DNS Records
      blockinfile:
        path: /etc/bind/zones/projeto.com.zone
        content: |
          mail    IN      A       192.168.117.133
          @       IN      MX 10   mail.projeto.com.
          @       IN      TXT     "v=spf1 mx -all"
      register: dns_records_change
      changed_when: dns_records_change.changed
      check_mode: no
      diff: yes

    - name: Restart the BIND9 Service
      systemd:
        name: bind9
        state: restarted
```

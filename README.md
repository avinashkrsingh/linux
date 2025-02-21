    - name: Copy bien, ctel, and cefg folders to /opt/middleware/ee/
      ansible.builtin.copy:
        src: "/path/to/source/{{ item }}/"
        dest: "/opt/e/e/{{ item }}/"
        mode: '0755'
      loop:
        - binq
        - ctlw
        - cfgw

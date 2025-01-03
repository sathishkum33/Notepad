- name: Remove the first line starting with "\c. <any name>" from multiple files
  hosts: localhost
  tasks:
    - name: Check first line of each file
      command: head -n 1 "{{ item }}"
      register: first_line
      with_items:
        - /path/to/file1.sql
        - /path/to/file2.sql

    - name: Debug the first line for validation
      debug:
        msg: "First line of {{ item.item }} is '{{ item.stdout }}'"
      with_items: "{{ first_line.results }}"

    - name: Remove the first line if it starts with "\c"
      lineinfile:
        path: "{{ item }}"
        regexp: '^\\c\\..*'
        state: absent
      when: first_line.results[loop.index0].stdout.startswith('\\c')
      with_items:
        - /path/to/file1.sql
        - /path/to/file2.sql
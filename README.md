# Common-model-for-Codeiginter
```php

<?php
class Common_Model extends CI_Model {

    function __construct() {
        parent::__construct();
        $this->load->library('form_validation');
    }
	

	
	public function select_data($table, $data='', $where, $order_by='', $order='', $orWhere='',$limit='',$like='', $orlike='',$group_by='',$where_in='')
    {   
	   
		$data = ($data)?$data:'*';
        $this->db->select($data);
		$this->db->from($table); 
		 
			
		
        if ($where)  $this->db->where($where);
       
		if ($orWhere) $this->db->or_where($orWhere);
		
		if ($like) $this->db->like($like);
		
		if ($orlike) $this->db->like($orlike);
       
		if ($order) $this->db->order_by($order_by, $order);
       
		if($limit) $this->db->limit($limit);
		
		if($where_in) $this->db->where_in($where_in[0],$where_in[1]);
		
		
		
		$query = $this->db->get();
      
        if ($query->num_rows() > 0) {
			return $query->result();
            //return $query;
        } else {
            return false;
        }
        return true;
    }
	
	
	
	
	public function getSingleDataWithSpeFields($tbl_name, $condition='',$fldName='',$order_f='',$order_key='',$limit='') {
	
		if($fldName)$select=$fldName; else $select='*';
		$this->db->select($fldName);
		$this->db->from($tbl_name);
		if($condition)	$this->db->where($condition);
		if($order_f) $this->db->order_by($order_f,$order_key);
		$this->db->limit(1);
		$query = $this->db->get();
		if($query->num_rows() > 0){
			return $query->row();
		}else{
			return false;
		}
	}
	
	public function select_single_field_value($table, $field, $where, $order_by='', $order='')
    {
		if( $table!='' &&  $field!='' && !empty($where) ){
			
			$this->db->select($field);
			$this->db->where($where);
			$query = $this->db->get($table)->row();
			if ($order) $this->db->order_by($order_by, $order);
			
			if (!empty($query)) {
				return $query->$field;
			} else {
				return false;
			}
			
		}else{
		    return false;
		}
    }
	
	public function select_data_wherein($table, $data='', $where, $limit='',$where_inf='',$where_in='')
    {   
	    if($where_in) $model_ids = explode(",",$where_in); 
		$data = ($data)?$data:'*';
        $this->db->select($data);
		$this->db->from($table);
		
        if ($where)  $this->db->where($where);
		       
		if ($order) $this->db->order_by($order_by, $order);
       
		if($limit) $this->db->limit($limit);
		
		if($where_inf!='' && !empty($where_in)) $this->db->where_in($where_inf,$model_ids);
		
		$query = $this->db->get();
      
        if ($query->num_rows() > 0) {
			return $query->result();
            //return $query;
        } else {
            return false;
        }
        return true;
    }

	public function countData($tbleName='',$condition='',$orcondition='',$field='', $limit='',$like='') {
	
		$this->db->select($field);
		$this->db->from($tbleName);
		if($condition)	$this->db->where($condition);
		if($orcondition)	$this->db->or_where($orcondition);
		if($limit) $this->db->limit($limit);
		//$this->db->order_by('id', 'ASC');
		$query = $this->db->get();
		//echo $this->db->last_query(); 
		if($query->num_rows() > 0):
			return $query->num_rows();
		else:
			return false;
		endif;
	}
	 
	
	
	public function getIdBySlug($tbl_name, $condition='',$fldName='') {
	
		if($fldName)$select=$fldName; else $select='*';
		$this->db->select($fldName); 
		$this->db->from($tbl_name);
		if($condition)	$this->db->where($condition);
		$query = $this->db->get();
		if($query->num_rows() > 0){
			$data = $query->row();
			return $data->$fldName;
		}else{
			return false;
		}
	}
	
	
	public function getcountrow($tbl_name, $condition='',$fldName='') {
	
		if($fldName)$select=$fldName; else $select='*';
		$this->db->select($fldName);
		$this->db->from($tbl_name);
		if($condition)	$this->db->where($condition);
		$query = $this->db->get();
		if($query->num_rows() > 0){
			$data = $query->row();
			if($fldName){
				return $data->$fldName;
			}else{
				return $data; 
			}
			
		}else{
			return false;
		}
	}
	
	
	//get sum data
	public function getSumData($tbl_name,$singfldName,$condition='') {
	
		if($singfldName!='' && $tbl_name!='') { 
			$this->db->select_sum($singfldName);
			$this->db->from($tbl_name);
			if($condition)	$this->db->where($condition);
			$query = $this->db->get();
			if($query->num_rows() > 0){
				$data = $query->row();
				return $data->$singfldName;
			}else{
				return false; 
			}
		}else{
			return false;
		}
	}
	
	
    public function delete_data($table, $where)
    {
        $delete = false;
        $this->db->where($where);
        $delete = $this->db->delete($table);
        if ($delete) {
            return true;
        } else {
            return false;
        }
    }
	
    public function save_data($table, $data)
    {
        $this->db->insert($table, $data);
		return $this->db->insert_id();
    }
	public function save_datas($table, $data)
    {
        $this->db->insert_batch($table, $data);
    }
    public function update_data($table, $data, $where)
    {
        $this->db->where($where);
        $this->db->update($table, $data);
        return true;
    }
 
	public function insert_data($dataArr=array(), $tbl_name='') {	
		$this->db->trans_start();		
		$this->db->set($dataArr);		
		$this->db->insert($tbl_name);		
		$insert_id = $this->db->insert_id();	
		$this->db->trans_complete();		
		return  $insert_id;	
	} 	
		

    function uploadFile($file = array(), $conditionArray = array()) {
        $fileName = '';
        $uploadFile = '';
        if (!empty($file) && !empty($conditionArray)) {
            $ab = key($file);
            if ($this->form_validation->run($this) != FALSE) {
                setMessage('Please select a file to upload', 'warning');
                redirect($conditionArray['redirect_url'], 'referesh');
            } else {
                $setting['upload_path'] = $conditionArray['path'];
                if (array_key_exists('extention', $conditionArray)) {
                    $setting['allowed_types'] = $conditionArray['extention'];
                }
                if (array_key_exists('max_width', $conditionArray)) {
                    $setting['max_width'] = $conditionArray['max_width'];
                }
                if (array_key_exists('max_height', $conditionArray)) {
                    $setting['max_height'] = $conditionArray['max_height'];
                }
                if (array_key_exists('size', $conditionArray)) {
                    $setting['max_size'] = $conditionArray['size'];
                }

                if (array_key_exists('min_width', $conditionArray)) {
                    $setting['min_width'] = $conditionArray['min_width'];
                }
                if (array_key_exists('min_height', $conditionArray)) {
                    $setting['min_height'] = $conditionArray['min_height'];
                }

                $this->load->library('upload', $setting);
                $this->upload->initialize($setting);
                if (!$this->upload->do_upload($ab)) {
                    setMessage($this->upload->display_errors(), 'warning');
                    redirect($conditionArray['redirect_url'], 'referesh');
                } else {
                    $upload_data = $this->upload->data();
                    $fileName = $upload_data['file_name'];
                    $image_path = $upload_data['full_path'];
                    try {
                        chmod($image_path, 0777);
                    } catch (Exception $e) {
                        log_message('file permession: ', $e->getMessage());
                    }
                }

                return $fileName;
            }
        }
        return false;
    }

    function deleteCustomeAttribute($tableName = null, $coloum = null, $id = null, $extraCondition = array()) {
        if (!empty($tableName) && !empty($coloum)) {
            $this->db->where($coloum, $id);
            $res = $this->db->delete($tableName); 
            if ($res) {
                return true;
            }
            return false;
        }else{			return false;		} 
    }

   
	function do_upload_data($con, $imagename){
		
		$config['upload_path']          = $con;
        $config['allowed_types']        = '*';
        $config['max_size']             = 0;
		$config['encrypt_name'] 		= TRUE;			 
	            
         $this->load->library('upload', $config,'life_license_upload');
		 $this->life_license_upload->initialize($config);
         if ( ! $this->life_license_upload->do_upload($imagename))
         {
              $error = array('error' => $this->life_license_upload->display_errors());
				
          }
          else
          {
            $data = array('upload' => $this->life_license_upload->data());
			return $data['upload']['file_name']; 
		  }

	}
	
	
	function do_upload_data_explain($con, $imagename){
		
		$config['upload_path']          = $con;
        $config['allowed_types']        = '*';
        $config['max_size']             = 0;
		$config['encrypt_name'] 		= FALSE;
			
	            
         $this->load->library('upload', $config);
		 $this->upload->initialize($config);
         if ( ! $this->upload->do_upload($imagename))
         {
              $error = array('error' => $this->upload->display_errors());
				
          }
          else
          {
            //$data = array('upload' => $this->upload->data());
			//return all image data			
			return $this->upload->data(); 
		  }

	}
	
	function multi_upload_data_explain($con, $imagename) {
		
		
		$config['upload_path']          = $con;
        $config['allowed_types']        = '*';
        $config['max_size']             = 0;
		$config['encrypt_name'] 		= FALSE;
			
			$this->load->library('upload', $config);
		 $this->upload->initialize($config);
         
			$dat = $this->upload->do_upload($imagename);
		return $dat;
		
			
         /* $this->load->library('upload', $config);
		 $this->upload->initialize($config);
         if ( ! $this->upload->do_upload($imagename))
         {
              $error = array('error' => $this->upload->display_errors());
				
          }
          else
          {
            //$data = array('upload' => $this->upload->data());
			//return all image data			
			return $this->upload->data(); 
		  } */
		
	}   

   
 
	//send mail function 
	
	function send_mail($tox,$subjectx,$bodyx){
		
		 
		// Set SMTP Configuration
				$emailConfig = [  
					'protocol' => 'ssmtp',
					'smtp_host' => 'smtp.googlemail.com',
					'smtp_port' => 587,
					'smtp_user' => 'your email id',
					'smtp_pass' => 'your password',
					'_smtp_auth' => TRUE,
					'charset'   => 'utf-8',
					'send_multipart'=> FALSE
				];
				 
				$subject = $subjectx;
			  //  $message = 'Type your gmail message here'; // use this line to send text email.
				// load view file called "welcome_message" in to a $message variable as a html string.
				$message =  $bodyx; 
				// Load CodeIgniter Email library 
				$this->load->library('email',$emailConfig); 
				// Sometimes you have to set the new line character for better result
				$this->email->set_newline("\r\n");
				// Set email preferences 
				$this->email->from('Your email id');
				$this->email->to($tox);
				$this->email->subject($subject);
				$this->email->message($message);
				$this->email->set_mailtype('html');
				// Ready to send email and check whether the email was successfully sent
				
				if (!$this->email->send()) {
					// Raise error message
					show_error($this->email->print_debugger());
					
				} else {
					// Show success notification or other things here
					//return 'Success';
				}
		
	}
	




}



```
